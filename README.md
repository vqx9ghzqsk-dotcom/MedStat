<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>MedRef RDC — Migration Firestore</title>
<style>
  body { font-family: system-ui, -apple-system, sans-serif; background: #f9fafb; padding: 20px; color: #1f2937; max-width: 800px; margin: 0 auto; }
  h1 { color: #047857; }
  .box { background: white; border: 1px solid #e5e7eb; border-radius: 8px; padding: 20px; margin-bottom: 16px; }
  .box h2 { font-size: 1.05rem; margin-bottom: 12px; color: #065f46; }
  button { background: #059669; color: white; border: none; padding: 10px 16px; border-radius: 6px; cursor: pointer; font-size: 0.88rem; }
  button:hover { background: #047857; }
  button:disabled { background: #9ca3af; cursor: not-allowed; }
  pre { background: #f3f4f6; padding: 10px; border-radius: 6px; font-size: 0.78rem; overflow-x: auto; max-height: 200px; overflow-y: auto; }
  .log { font-family: monospace; font-size: 0.8rem; line-height: 1.6; }
  .ok { color: #047857; }
  .err { color: #b91c1c; }
  .warn { color: #92400e; }
  input { padding: 8px 12px; border: 1px solid #d1d5db; border-radius: 6px; font-size: 0.88rem; width: 100%; margin-bottom: 8px; box-sizing: border-box; }
  .form-hint { font-size: 0.78rem; color: #6b7280; margin-bottom: 12px; }
</style>
</head>
<body>

<h1>Migration des pathologies vers Firestore</h1>

<div class="box">
  <h2>1. Connexion administrateur</h2>
  <p class="form-hint">Connectez-vous avec un compte administrateur. La migration écrit dans la collection <code>pathologies</code>.</p>
  <input type="email" id="email" placeholder="admin@medref-rdc.org">
  <input type="password" id="password" placeholder="Mot de passe">
  <button id="loginBtn" onclick="doLogin()">Se connecter</button>
  <div id="loginStatus" class="log" style="margin-top:8px"></div>
</div>

<div class="box">
  <h2>2. Envoyer les pathologies vers Firestore</h2>
  <p class="form-hint">Cliquez pour envoyer toutes les pathologies du JSON inline vers Firestore. Les documents existants (même ID) seront fusionnés (merge). Les documents non présents dans le JSON seront conservés.</p>
  <button id="migrateBtn" onclick="doMigrate()" disabled>Envoyer les pathologies</button>
  <div id="migrateLog" class="log" style="margin-top:12px"></div>
</div>

<div class="box">
  <h2>3. Donner les droits admin à un utilisateur</h2>
  <p class="form-hint">Si vous venez de créer un compte utilisateur dans Firebase Auth, vous pouvez lui donner les droits admin en ajoutant son UID à la collection <code>admins</code>. Saisissez son UID (visible dans Firebase Console &gt; Authentication) :</p>
  <input type="text" id="adminUid" placeholder="UID de l'utilisateur (ex: abc123def456)">
  <button id="setAdminBtn" onclick="setAdmin()" disabled>Donner les droits admin</button>
  <div id="setAdminLog" class="log" style="margin-top:8px"></div>
</div>

<div class="box">
  <h2>4. Vérifier</h2>
  <p class="form-hint">Liste les documents dans la collection <code>pathologies</code>.</p>
  <button id="listBtn" onclick="doList()" disabled>Lister les pathologies</button>
  <pre id="listOutput"></pre>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, doc, setDoc, getDoc, getDocs, collection } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
import { getAuth, signInWithEmailAndPassword, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";

const firebaseConfig = {
  apiKey: "AIzaSyAp-LHy_8FHaOLr-7pNyERco_ioCnP1ncw",
  authDomain: "medref-rdc.firebaseapp.com",
  projectId: "medref-rdc",
  storageBucket: "medref-rdc.firebasestorage.app",
  messagingSenderId: "112788481626",
  appId: "1:112788481626:web:f42965ceb89173655e9a63",
  measurementId: "G-RK414HTRZB"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const auth = getAuth(app);

let currentUser = null;
let currentUid = null;

onAuthStateChanged(auth, (user) => {
  currentUser = user;
  if (user) {
    currentUid = user.uid;
    document.getElementById('loginStatus').innerHTML = '<span class="ok">✓ Connecté : ' + user.email + ' (UID: ' + user.uid + ')</span>';
    document.getElementById('loginBtn').textContent = 'Déconnecter';
    document.getElementById('loginBtn').onclick = () => auth.signOut();
    document.getElementById('migrateBtn').disabled = false;
    document.getElementById('listBtn').disabled = false;
    document.getElementById('setAdminBtn').disabled = false;
  } else {
    document.getElementById('loginStatus').innerHTML = '<span class="warn">Non connecté.</span>';
    document.getElementById('loginBtn').textContent = 'Se connecter';
    document.getElementById('loginBtn').onclick = doLogin;
    document.getElementById('migrateBtn').disabled = true;
    document.getElementById('listBtn').disabled = true;
    document.getElementById('setAdminBtn').disabled = true;
  }
});

window.doLogin = async function() {
  const email = document.getElementById('email').value.trim();
  const password = document.getElementById('password').value;
  if (!email || !password) { alert('Email et mot de passe requis'); return; }
  try {
    await signInWithEmailAndPassword(auth, email, password);
  } catch (e) {
    document.getElementById('loginStatus').innerHTML = '<span class="err">Erreur : ' + e.message + '</span>';
  }
};

window.doMigrate = async function() {
  const log = document.getElementById('migrateLog');
  log.innerHTML = '<span class="warn">Lecture du JSON inline…</span>';
  try {
    // Fetch the main HTML to extract the inline JSON
    const res = await fetch('medref-rdc.html');
    const html = await res.text();
    const m = html.match(/<script id="pathology-data"[^>]*>([\s\S]*?)<\/script>/);
    if (!m) { log.innerHTML = '<span class="err">JSON inline introuvable dans medref-rdc.html.</span>'; return; }
    const data = JSON.parse(m[1]);
    log.innerHTML = '<span class="warn">' + data.length + ' pathologies à envoyer…</span>';

    let ok = 0, err = 0;
    for (const p of data) {
      try {
        await setDoc(doc(db, 'pathologies', p.id), p, { merge: true });
        ok++;
        if (ok % 5 === 0) log.innerHTML = '<span class="warn">En cours… ' + ok + '/' + data.length + '</span>';
      } catch (e) {
        err++;
        log.innerHTML += '<div class="err">✗ ' + p.id + ' : ' + e.message + '</div>';
      }
    }
    log.innerHTML = '<span class="ok">✓ Migration terminée : ' + ok + ' envoyées, ' + err + ' erreur(s).</span>';
  } catch (e) {
    log.innerHTML = '<span class="err">Erreur : ' + e.message + '</span>';
  }
};

window.setAdmin = async function() {
  const uid = document.getElementById('adminUid').value.trim();
  const log = document.getElementById('setAdminLog');
  if (!uid) { log.innerHTML = '<span class="err">UID requis.</span>'; return; }
  try {
    await setDoc(doc(db, 'admins', uid), { admin: true, role: 'admin', addedAt: new Date().toISOString(), addedBy: currentUser.email });
    log.innerHTML = '<span class="ok">✓ UID ' + uid + ' ajouté aux admins. L\'utilisateur peut maintenant se connecter au site principal en mode admin.</span>';
  } catch (e) {
    log.innerHTML = '<span class="err">Erreur : ' + e.message + '</span>';
  }
};

window.doList = async function() {
  const out = document.getElementById('listOutput');
  out.textContent = 'Chargement…';
  try {
    const snap = await getDocs(collection(db, 'pathologies'));
    if (snap.empty) { out.textContent = 'Aucune pathologie dans Firestore.'; return; }
    const lines = [];
    snap.forEach(d => {
      const data = d.data();
      lines.push(d.id + ' : ' + (data.name || '(sans nom)') + ' [' + (data.departmentId || '?') + '/' + (data.courseId || '?') + ']');
    });
    out.textContent = lines.join('\n') + '\n\nTotal : ' + lines.length + ' pathologies.';
  } catch (e) {
    out.textContent = 'Erreur : ' + e.message;
  }
};
</script>
</body>
</html>
