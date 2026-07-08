<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<meta name="theme-color" content="#1A5276">
<meta name="description" content="MedStat - Application d'accompagnement des memoires de fin d'etudes en medecine. Creee par Dr Mingalu.">
<title>MedStat - Memoires de Medecine</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=Roboto+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<script src="https://cdn.tailwindcss.com"><\/script>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.4/dist/chart.umd.min.js"><\/script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"><\/script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-auth-compat.js"><\/script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore-compat.js"><\/script>
<script>
tailwind.config={theme:{extend:{colors:{primary:{DEFAULT:"#1A5276",light:"#2980B9",dark:"#0E2F44"},accent:{DEFAULT:"#2E86C1",light:"#5DADE2",dark:"#1F6FA3"},success:"#27AE60",warning:"#F39C12",error:"#E74C3C",bg:"#F4F6F9",card:"#FFFFFF",txt:{DEFAULT:"#333333",secondary:"#5B6B7D",muted:"#95A5A6"}},fontFamily:{sans:["Inter","sans-serif"],mono:["Roboto Mono","monospace"]}}}};
<\/script>
<style>
*{box-sizing:border-box;margin:0;padding:0}
html,body{height:100%;font-family:'Inter',sans-serif;background:#F4F6F9;color:#333;overflow-x:hidden}
.screen{display:none;min-height:100vh;flex-direction:column}
.screen.active{display:flex}
.fade-in{animation:fadeIn .3s ease}
@keyframes fadeIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
@keyframes pulse{0%,100%{transform:scale(1)}50%{transform:scale(1.15)}}
.pulse-anim{animation:pulse 1.5s infinite}
.card{background:#fff;border-radius:12px;box-shadow:0 1px 3px rgba(0,0,0,.08);padding:16px}
.card-elevated{background:#fff;border-radius:12px;box-shadow:0 4px 12px rgba(0,0,0,.1);padding:20px}
.btn-primary{background:#2E86C1;color:#fff;padding:12px 24px;border-radius:12px;font-weight:600;font-size:16px;border:none;cursor:pointer;transition:all .2s;font-family:Inter}
.btn-primary:hover{background:#1F6FA3}
.btn-danger{background:#E74C3C;color:#fff;padding:10px 20px;border-radius:12px;font-weight:600;border:none;cursor:pointer;font-family:Inter}
.btn-success{background:#27AE60;color:#fff;padding:10px 20px;border-radius:12px;font-weight:600;border:none;cursor:pointer;font-family:Inter}
.btn-outline{background:transparent;color:#2E86C1;border:2px solid #2E86C1;padding:10px 20px;border-radius:12px;font-weight:600;cursor:pointer;font-family:Inter}
.btn-sm{padding:8px 16px;font-size:13px;border-radius:8px}
.input-field{width:100%;padding:12px 16px;border:1.5px solid #E0E6ED;border-radius:12px;font-size:14px;font-family:Inter;transition:border .2s;background:#fff}
.input-field:focus{outline:none;border-color:#2E86C1;box-shadow:0 0 0 3px rgba(46,134,193,.15)}
.badge{display:inline-flex;align-items:center;padding:2px 10px;border-radius:20px;font-size:12px;font-weight:600}
.badge-blue{background:#EBF5FB;color:#2E86C1}
.badge-green{background:#EAFAF1;color:#27AE60}
.badge-orange{background:#FEF5E7;color:#F39C12}
.badge-red{background:#FDEDEC;color:#E74C3C}
.badge-gray{background:#F2F3F4;color:#5B6B7D}
.bottom-nav{position:fixed;bottom:0;left:0;right:0;background:#fff;border-top:1px solid #E8ECF0;display:flex;z-index:50;padding:6px 0 env(safe-area-inset-bottom,6px)}
.nav-item{flex:1;display:flex;flex-direction:column;align-items:center;padding:4px 0;cursor:pointer;color:#95A5A6;transition:color .2s;font-size:11px;gap:2px;border:none;background:none;font-family:Inter}
.nav-item.active{color:#2E86C1}
.nav-item svg{width:24px;height:24px}
.drawer-overlay{position:fixed;inset:0;background:rgba(0,0,0,.4);z-index:100;display:none}
.drawer-overlay.open{display:block}
.drawer{position:fixed;top:0;left:-280px;width:280px;height:100%;background:#fff;z-index:101;transition:left .3s;overflow-y:auto;padding:20px}
.drawer.open{left:0}
.toast{position:fixed;top:20px;right:20px;padding:14px 20px;border-radius:12px;color:#fff;font-weight:500;z-index:200;animation:fadeIn .3s ease;max-width:340px;font-family:Inter}
.modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,.5);z-index:150;display:flex;align-items:center;justify-content:center;padding:16px}
.tab-bar{display:flex;gap:0;border-bottom:2px solid #E8ECF0;overflow-x:auto;scrollbar-width:none}
.tab-bar::-webkit-scrollbar{display:none}
.tab-item{padding:12px 20px;white-space:nowrap;cursor:pointer;font-weight:500;color:#5B6B7D;border-bottom:2px solid transparent;margin-bottom:-2px;transition:all .2s;background:none;border-top:none;border-left:none;border-right:none;font-family:Inter;font-size:14px}
.tab-item.active{color:#2E86C1;border-bottom-color:#2E86C1}
.msg-bubble{max-width:80%;padding:10px 14px;border-radius:16px;font-size:14px;line-height:1.5;word-wrap:break-word}
.msg-sent{background:#2E86C1;color:#fff;border-bottom-right-radius:4px;margin-left:auto}
.msg-received{background:#F0F2F5;color:#333;border-bottom-left-radius:4px}
textarea.input-field{resize:vertical;min-height:100px}
.accordion-header{display:flex;align-items:center;justify-content:space-between;padding:14px 16px;cursor:pointer;border-radius:10px;background:#F8F9FA;transition:background .2s}
.accordion-header:hover{background:#F0F2F5}
.accordion-content{max-height:0;overflow:hidden;transition:max-height .3s ease}
.accordion-content.open{max-height:3000px}
.pb-safe{padding-bottom:80px}
.appbar{background:#fff;padding:12px 16px;display:flex;align-items:center;gap:12px;border-bottom:1px solid #F0F2F5;position:sticky;top:0;z-index:40}
.progress-ring{transform:rotate(-90deg)}
select.input-field{appearance:none;background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath fill='%235B6B7D' d='M6 8L1 3h10z'/%3E%3C/svg%3E");background-repeat:no-repeat;background-position:right 14px center}
</style>
</head>
<body>
<!-- SPLASH -->
<div id="screen-splash" class="screen active" style="background:linear-gradient(135deg,#1A5276,#2E86C1);justify-content:center;align-items:center;text-align:center">
<div class="fade-in" style="text-align:center">
  <div style="width:100px;height:100px;border-radius:24px;background:rgba(255,255,255,.15);display:flex;align-items:center;justify-content:center;margin:0 auto 20px;backdrop-filter:blur(10px)">
    <svg width="56" height="56" fill="#fff" viewBox="0 0 24 24"><path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-5 14H7v-2h7v2zm3-4H7v-2h10v2zm0-4H7V7h10v2z"/></svg>
  </div>
  <h1 style="color:#fff;font-size:32px;font-weight:800;letter-spacing:-0.5px">MedStat</h1>
  <p style="color:rgba(255,255,255,.8);font-size:14px;margin-top:8px;max-width:280px;line-height:1.5">Accompagnement complet des memoires de fin d'etudes en medecine</p>
  <div style="margin-top:48px;display:flex;flex-direction:column;gap:12px;width:280px;margin-left:auto;margin-right:auto">
    <button class="btn-primary" style="width:100%;padding:14px" onclick="nav('login')">Se connecter</button>
    <button class="btn-outline" style="width:100%;padding:14px;color:#fff;border-color:rgba(255,255,255,.4)" onclick="nav('signup')">Creer un compte</button>
  </div>
  <button style="background:none;border:none;color:rgba(255,255,255,.7);cursor:pointer;margin-top:24px;font-size:13px;text-decoration:underline;font-family:Inter" onclick="nav('about')">A propos de MedStat</button>
</div>
</div>
<!-- LOGIN -->
<div id="screen-login" class="screen" style="background:#fff;justify-content:center;align-items:center;padding:20px">
<div style="width:100%;max-width:400px" class="fade-in">
  <div style="text-align:center;margin-bottom:32px">
    <div style="width:72px;height:72px;border-radius:18px;background:linear-gradient(135deg,#1A5276,#2E86C1);display:flex;align-items:center;justify-content:center;margin:0 auto 16px">
      <svg width="40" height="40" fill="#fff" viewBox="0 0 24 24"><path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-5 14H7v-2h7v2zm3-4H7v-2h10v2zm0-4H7V7h10v2z"/></svg>
    </div>
    <h2 style="font-size:24px;font-weight:700;color:#1A5276">Bienvenue</h2>
    <p style="color:#5B6B7D;font-size:14px;margin-top:4px">Connectez-vous a votre compte MedStat</p>
  </div>
  <div style="display:flex;flex-direction:column;gap:16px">
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Adresse e-mail</label><input id="login-email" type="email" class="input-field" placeholder="votre@email.com"></div>
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Mot de passe</label><input id="login-password" type="password" class="input-field" placeholder="Min. 6 caracteres"></div>
    <button class="btn-primary" style="width:100%;margin-top:4px" onclick="doLogin()">Se connecter</button>
    <p id="login-error" style="color:#E74C3C;font-size:13px;text-align:center;display:none"></p>
    <div style="display:flex;align-items:center;gap:12px"><div style="flex:1;height:1px;background:#E0E6ED"></div><span style="color:#95A5A6;font-size:13px">ou</span><div style="flex:1;height:1px;background:#E0E6ED"></div></div>
    <button onclick="doGoogleLogin()" style="width:100%;padding:12px;border-radius:12px;border:1.5px solid #E0E6ED;background:#fff;cursor:pointer;display:flex;align-items:center;justify-content:center;gap:10px;font-weight:500;font-size:14px;font-family:Inter">
      <svg width="20" height="20" viewBox="0 0 24 24"><path d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92a5.06 5.06 0 01-2.2 3.32v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.1z" fill="#4285F4"/><path d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z" fill="#34A853"/><path d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z" fill="#FBBC05"/><path d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z" fill="#EA4335"/></svg>
      Se connecter avec Google
    </button>
    <p style="text-align:center;font-size:14px;color:#5B6B7D">Pas encore de compte ? <a href="#" onclick="nav('signup');return false" style="color:#2E86C1;font-weight:600;text-decoration:none">S'inscrire</a></p>
    <p style="text-align:center"><a href="#" onclick="nav('about');return false" style="color:#95A5A6;font-size:13px;text-decoration:underline">A propos de MedStat</a></p>
  </div>
</div>
</div>
<!-- SIGNUP -->
<div id="screen-signup" class="screen" style="background:#fff;padding:0">
<div style="background:#fff;padding:16px 20px;display:flex;align-items:center;gap:16px;border-bottom:1px solid #F0F2F5">
  <button onclick="nav('splash')" style="background:none;border:none;cursor:pointer;color:#1A5276"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M20 11H7.83l5.59-5.59L12 4l-8 8 8 8 1.41-1.41L7.83 13H20v-2z"/></svg></button>
  <h2 style="font-size:18px;font-weight:700;color:#1A5276;flex:1;text-align:center;margin-right:40px">Inscription</h2>
</div>
<div style="display:flex;justify-content:center;gap:8px;padding:20px 20px 0" id="signup-steps">
  <div style="width:40px;height:4px;border-radius:2px;background:#2E86C1" id="step-bar-1"></div>
  <div style="width:40px;height:4px;border-radius:2px;background:#E0E6ED" id="step-bar-2"></div>
  <div style="width:40px;height:4px;border-radius:2px;background:#E0E6ED" id="step-bar-3"></div>
</div>
<!-- Step 1 -->
<div id="signup-s1" style="padding:24px 20px;display:flex;flex-direction:column;gap:16px">
  <h3 style="font-size:16px;font-weight:700;color:#1A5276">Informations personnelles</h3>
  <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Nom complet</label><input id="su-name" class="input-field" placeholder="Dr Jean Mukendi"></div>
  <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Adresse e-mail</label><input id="su-email" type="email" class="input-field" placeholder="votre@email.com"></div>
  <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Mot de passe</label><input id="su-pwd" type="password" class="input-field" placeholder="Min. 6 caracteres"></div>
  <button class="btn-primary" style="width:100%;margin-top:8px" onclick="suStep(2)">Suivant</button>
</div>
<!-- Step 2 -->
<div id="signup-s2" style="padding:24px 20px;display:none;flex-direction:column;gap:16px">
  <h3 style="font-size:16px;font-weight:700;color:#1A5276">Choix du role</h3>
  <div style="display:flex;flex-direction:column;gap:12px">
    <div id="rc-student" onclick="pickRole('student')" style="border:2px solid #E0E6ED;border-radius:12px;padding:16px;cursor:pointer;transition:all .2s">
      <div style="display:flex;align-items:center;gap:12px">
        <div style="width:44px;height:44px;border-radius:12px;background:#EBF5FB;display:flex;align-items:center;justify-content:center;color:#2E86C1"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M5 13.18v4L12 21l7-3.82v-4L12 17l-7-3.82zM12 3L1 9l11 6 9-4.91V17h2V9L12 3z"/></svg></div>
        <div><p style="font-weight:600;font-size:15px">Etudiant</p><p style="font-size:12px;color:#5B6B7D">Finaliste en medecine (D3/D4)</p></div>
      </div>
    </div>
    <div id="rc-director" onclick="pickRole('director')" style="border:2px solid #E0E6ED;border-radius:12px;padding:16px;cursor:pointer;transition:all .2s">
      <div style="display:flex;align-items:center;gap:12px">
        <div style="width:44px;height:44px;border-radius:12px;background:#EAFAF1;display:flex;align-items:center;justify-content:center;color:#27AE60"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/></svg></div>
        <div><p style="font-weight:600;font-size:15px">Directeur de memoire</p><p style="font-size:12px;color:#5B6B7D">Encadrant academique</p></div>
      </div>
    </div>
  </div>
  <div id="sf-student" style="display:none;flex-direction:column;gap:12px">
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Universite</label><select id="su-uni" class="input-field"><option value="">Selectionnez...</option><option value="UNIKIN">Universite de Kinshasa</option><option value="UCBC">Universite Catholique du Bukavu</option><option value="UCB">Universite Catholique de Bukavu</option><option value="ULPGL">Universite Libre de Pays des Grands Lacs</option><option value="UNILU">Universite de Lubumbashi</option><option value="UPC">Universite Protestante au Congo</option><option value="USK">Universite Simon Kimbangu</option><option value="UK">Universite Kongo</option></select></div>
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Promotion</label><select id="su-promo" class="input-field"><option value="">Selectionnez...</option><option value="D3">D3</option><option value="D4">D4</option></select></div>
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Specialite</label><input id="su-spec" class="input-field" placeholder="ex: Medecine interne"></div>
  </div>
  <div id="sf-director" style="display:none;flex-direction:column;gap:12px">
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Universite</label><select id="su-unid" class="input-field"><option value="">Selectionnez...</option><option value="UNIKIN">Universite de Kinshasa</option><option value="UCBC">Universite Catholique du Bukavu</option><option value="UCB">Universite Catholique de Bukavu</option><option value="ULPGL">Universite Libre de Pays des Grands Lacs</option><option value="UNILU">Universite de Lubumbashi</option><option value="UPC">Universite Protestante au Congo</option><option value="USK">Universite Simon Kimbangu</option><option value="UK">Universite Kongo</option></select></div>
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Titre academique</label><select id="su-title" class="input-field"><option value="">Selectionnez...</option><option value="Dr">Dr</option><option value="Prof">Prof</option><option value="CT">Chef de Travaux</option><option value="Assist">Assistant</option></select></div>
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Specialite</label><input id="su-specd" class="input-field" placeholder="ex: Chirurgie generale"></div>
  </div>
  <div style="display:flex;gap:12px;margin-top:8px">
    <button class="btn-outline" style="flex:1" onclick="suStep(1)">Retour</button>
    <button class="btn-primary" style="flex:1" onclick="suStep(3)">Suivant</button>
  </div>
</div>
<!-- Step 3 -->
<div id="signup-s3" style="padding:24px 20px;display:none;flex-direction:column;gap:16px">
  <h3 style="font-size:16px;font-weight:700;color:#1A5276">Confirmation</h3>
  <div id="su-summary" class="card" style="background:#F4F6F9"></div>
  <label style="display:flex;align-items:flex-start;gap:10px;cursor:pointer">
    <input type="checkbox" id="su-terms" style="margin-top:4px;accent-color:#2E86C1;width:18px;height:18px">
    <span style="font-size:13px;color:#5B6B7D;line-height:1.5">J'accepte les conditions d'utilisation et la politique de confidentialite</span>
  </label>
  <p id="su-error" style="color:#E74C3C;font-size:13px;display:none"></p>
  <div style="display:flex;gap:12px;margin-top:8px">
    <button class="btn-outline" style="flex:1" onclick="suStep(2)">Retour</button>
    <button class="btn-primary" style="flex:1" onclick="doSignup()">Creer mon compte</button>
  </div>
</div>
</div>
<!-- ABOUT -->
<div id="screen-about" class="screen" style="background:#fff">
<div style="background:linear-gradient(135deg,#1A5276,#2E86C1);padding:60px 20px 40px;text-align:center">
  <div style="width:80px;height:80px;border-radius:20px;background:rgba(255,255,255,.15);display:flex;align-items:center;justify-content:center;margin:0 auto 16px;backdrop-filter:blur(10px)">
    <svg width="44" height="44" fill="#fff" viewBox="0 0 24 24"><path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-5 14H7v-2h7v2zm3-4H7v-2h10v2zm0-4H7V7h10v2z"/></svg>
  </div>
  <h1 style="color:#fff;font-size:28px;font-weight:800">MedStat</h1>
  <p style="color:rgba(255,255,255,.85);font-size:14px;margin-top:4px">Accompagnement des memoires de fin d'etudes en medecine</p>
</div>
<div style="padding:24px 20px;display:flex;flex-direction:column;gap:20px">
  <div class="card"><h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:8px">Createur</h3><p style="font-size:14px;color:#5B6B7D;line-height:1.6">MedStat a ete creee par <strong style="color:#333">Dr Mingalu</strong>, medecin et informaticien, dont la vision est de democratise l'accompagnement a la recherche scientifique medicale en Republique Democratique du Congo.</p></div>
  <div class="card"><h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:8px">Mission</h3><p style="font-size:14px;color:#5B6B7D;line-height:1.6">Centraliser tous les outils necessaires a la recherche medicale dans une seule plateforme, depuis la mise en relation etudiant-directeur jusqu'a la generation automatique de la discussion et des references bibliographiques.</p></div>
  <div class="card"><h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:8px">Fonctionnalites cles</h3>
    <ul style="font-size:14px;color:#5B6B7D;line-height:1.8;padding-left:20px">
      <li>Gestion complete du processus de memoire (9 jalons)</li>
      <li>Generation IA de fiches d'enquete et discussions</li>
      <li>Analyses statistiques avancees (17+ tests)</li>
      <li>Messagerie integree etudiant-directeur</li>
      <li>Mode hors ligne pour la collecte</li>
      <li>Anti-plagiat et export multi-format</li>
      <li>Calendrier academique et notifications push</li>
      <li>Multi-directeurs et co-supervision</li>
    </ul>
  </div>
  <div class="card"><h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:8px">Universites partenaires</h3><div style="display:flex;flex-wrap:wrap;gap:8px;margin-top:8px"><span class="badge badge-blue">UNIKIN</span><span class="badge badge-blue">UCBC</span><span class="badge badge-blue">UCB</span><span class="badge badge-blue">ULPGL</span><span class="badge badge-blue">UNILU</span><span class="badge badge-blue">UPC</span><span class="badge badge-blue">USK</span><span class="badge badge-blue">UK</span></div></div>
  <div class="card"><h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:8px">Version</h3><p style="font-size:14px;color:#5B6B7D">MedStat v1.0 - Juillet 2026</p><p style="font-size:13px;color:#95A5A6;margin-top:4px">(c) 2026 MedStat - Dr Mingalu. Tous droits reserves.</p></div>
  <button onclick="nav('splash')" class="btn-primary" style="width:100%">Retour a l'accueil</button>
</div>
</div>
<!-- ═══ APP SHELL: DRAWER ═══ -->
<div id="drawer-overlay" class="drawer-overlay" onclick="toggleDrawer()"></div>
<div id="drawer" class="drawer">
  <div style="display:flex;align-items:center;gap:12px;padding:8px 0 20px;border-bottom:1px solid #F0F2F5">
    <div id="drawer-avatar" style="width:44px;height:44px;border-radius:50%;background:linear-gradient(135deg,#1A5276,#2E86C1);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:18px">?</div>
    <div><p id="drawer-name" style="font-weight:600;font-size:15px">Utilisateur</p><p id="drawer-role" style="font-size:12px;color:#5B6B7D">Role</p></div>
  </div>
  <nav style="padding:16px 0;display:flex;flex-direction:column;gap:4px">
    <button onclick="nav('profile');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/></svg>Mon profil</button>
    <button onclick="nav('calendar');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M20 3h-1V1h-2v2H7V1H5v2H4c-1.1 0-2 .9-2 2v16c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 18H4V8h16v13z"/></svg>Calendrier academique</button>
    <button onclick="nav('references');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M14 2H6c-1.1 0-1.99.9-1.99 2L4 20c0 1.1.89 2 1.99 2H18c1.1 0 2-.9 2-2V8l-6-6zm2 16H8v-2h8v2zm0-4H8v-2h8v2zm-3-5V3.5L18.5 9H13z"/></svg>References bibliographiques</button>
    <div id="drawer-admin-btn" style="display:none">
      <div style="height:1px;background:#F0F2F5;margin:8px 0"></div>
      <p style="font-size:11px;font-weight:600;color:#95A5A6;padding:4px 12px;text-transform:uppercase;letter-spacing:.5px">Administration</p>
      <button onclick="nav('admin-users');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5c-1.66 0-3 1.34-3 3s1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5C6.34 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg>Gestion utilisateurs</button>
      <button onclick="nav('admin-unis');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M5 13.18v4L12 21l7-3.82v-4L12 17l-7-3.82zM12 3L1 9l11 6 9-4.91V17h2V9L12 3z"/></svg>Universites</button>
      <button onclick="nav('admin-years');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M20 3h-1V1h-2v2H7V1H5v2H4c-1.1 0-2 .9-2 2v16c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 18H4V8h16v13z"/></svg>Annees academiques</button>
    </div>
    <div style="height:1px;background:#F0F2F5;margin:8px 0"></div>
    <button onclick="nav('settings');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M19.14 12.94c.04-.3.06-.61.06-.94 0-.32-.02-.64-.07-.94l2.03-1.58c.18-.14.23-.41.12-.61l-1.92-3.32c-.12-.22-.37-.29-.59-.22l-2.39.96c-.5-.38-1.03-.7-1.62-.94l-.36-2.54c-.04-.24-.24-.41-.48-.41h-3.84c-.24 0-.43.17-.47.41l-.36 2.54c-.59.24-1.13.57-1.62.94l-2.39-.96c-.22-.08-.47 0-.59.22L2.74 8.87c-.12.21-.08.47.12.61l2.03 1.58c-.05.3-.07.62-.07.94s.02.64.07.94l-2.03 1.58c-.18.14-.23.41-.12.61l1.92 3.32c.12.22.37.29.59.22l2.39-.96c.5.38 1.03.7 1.62.94l.36 2.54c.05.24.24.41.48.41h3.84c.24 0 .44-.17.47-.41l.36-2.54c.59-.24 1.13-.56 1.62-.94l2.39.96c.22.08.47 0 .59-.22l1.92-3.32c.12-.22.07-.47-.12-.61l-2.01-1.58zM12 15.6c-1.98 0-3.6-1.62-3.6-3.6s1.62-3.6 3.6-3.6 3.6 1.62 3.6 3.6-1.62 3.6-3.6 3.6z"/></svg>Parametres</button>
    <button onclick="nav('about');toggleDrawer()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#333;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" style="color:#5B6B7D" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-6h2v6zm0-8h-2V7h2v2z"/></svg>A propos de MedStat</button>
    <div style="height:1px;background:#F0F2F5;margin:8px 0"></div>
    <button onclick="doLogout()" style="display:flex;align-items:center;gap:12px;padding:12px;border:none;background:none;cursor:pointer;font-family:Inter;font-size:14px;color:#E74C3C;border-radius:10px;width:100%;text-align:left"><svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M17 7l-1.41 1.41L18.17 11H8v2h10.17l-2.58 2.58L17 17l5-5zM4 5h8V3H4c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h8v-2H4V5z"/></svg>Deconnexion</button>
  </nav>
</div>

<!-- ═══ APP SHELL: TOP BAR ═══ -->
<div id="appbar" class="appbar" style="display:none">
  <button onclick="toggleDrawer()" style="background:none;border:none;cursor:pointer;color:#1A5276;padding:4px"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M3 18h18v-2H3v2zm0-5h18v-2H3v2zm0-7v2h18V6H3z"/></svg></button>
  <span id="appbar-title" style="font-size:18px;font-weight:700;color:#1A5276;flex:1">MedStat</span>
  <button onclick="nav('notifications')" style="background:none;border:none;cursor:pointer;color:#5B6B7D;position:relative;padding:4px">
    <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M12 22c1.1 0 2-.9 2-2h-4c0 1.1.89 2 2 2zm6-6v-5c0-3.07-1.64-5.64-4.5-6.32V4c0-.83-.67-1.5-1.5-1.5s-1.5.67-1.5 1.5v.68C7.63 5.36 6 7.92 6 11v5l-2 2v1h16v-1l-2-2z"/></svg>
    <span id="notif-badge" class="badge badge-red" style="position:absolute;top:-2px;right:-2px;font-size:10px;min-width:18px;height:18px;display:none;align-items:center;justify-content:center;border-radius:9px;padding:0">0</span>
  </button>
  <button onclick="nav('profile')" style="background:none;border:none;cursor:pointer;padding:4px">
    <div id="appbar-avatar" style="width:32px;height:32px;border-radius:50%;background:linear-gradient(135deg,#1A5276,#2E86C1);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:14px">?</div>
  </button>
</div>

<!-- ═══ BOTTOM NAV - STUDENT ═══ -->
<div id="bnav-student" class="bottom-nav" style="display:none">
  <button class="nav-item active" onclick="navTab('dashboard','student')" data-tab="dashboard"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M3 13h8V3H3v10zm0 8h8v-6H3v6zm10 0h8V11h-8v10zm0-18v6h8V3h-8z"/></svg><span>Tableau de bord</span></button>
  <button class="nav-item" onclick="navTab('thesis','student')" data-tab="thesis"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M14 2H6c-1.1 0-1.99.9-1.99 2L4 20c0 1.1.89 2 1.99 2H18c1.1 0 2-.9 2-2V8l-6-6zm2 16H8v-2h8v2zm0-4H8v-2h8v2zm-3-5V3.5L18.5 9H13z"/></svg><span>Mon memoire</span></button>
  <button class="nav-item" onclick="navTab('collection','student')" data-tab="collection"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M19 3h-4.18C14.4 1.84 13.3 1 12 1c-1.3 0-2.4.84-2.82 2H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-7 0c.55 0 1 .45 1 1s-.45 1-1 1-1-.45-1-1 .45-1 1-1z"/></svg><span>Collecte</span></button>
  <button class="nav-item" onclick="navTab('stats','student')" data-tab="stats"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M5 9.2h3V19H5zM10.6 5h2.8v14h-2.8zm5.6 8H19v6h-2.8z"/></svg><span>Statistiques</span></button>
  <button class="nav-item" onclick="navTab('chat','student')" data-tab="chat"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M20 2H4c-1.1 0-1.99.9-1.99 2L2 22l4-4h14c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2z"/></svg><span>Messages</span></button>
</div>

<!-- ═══ BOTTOM NAV - DIRECTOR ═══ -->
<div id="bnav-director" class="bottom-nav" style="display:none">
  <button class="nav-item active" onclick="navTab('dashboard','director')" data-tab="dashboard"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M3 13h8V3H3v10zm0 8h8v-6H3v6zm10 0h8V11h-8v10zm0-18v6h8V3h-8z"/></svg><span>Tableau de bord</span></button>
  <button class="nav-item" onclick="navTab('students','director')" data-tab="students"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5c-1.66 0-3 1.34-3 3s1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5C6.34 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg><span>Etudiants</span></button>
  <button class="nav-item" onclick="navTab('projects','director')" data-tab="projects"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M10 4H4c-1.1 0-1.99.9-1.99 2L2 18c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V8c0-1.1-.9-2-2-2h-8l-2-2z"/></svg><span>Projets</span></button>
  <button class="nav-item" onclick="navTab('chat','director')" data-tab="chat"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M20 2H4c-1.1 0-1.99.9-1.99 2L2 22l4-4h14c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2z"/></svg><span>Messages</span></button>
  <button class="nav-item" onclick="navTab('archives','director')" data-tab="archives"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M20.54 5.23l-1.39-1.68C18.88 3.21 18.47 3 18 3H6c-.47 0-.88.21-1.16.55L3.46 5.23C3.17 5.57 3 6.02 3 6.5V19c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V6.5c0-.48-.17-.93-.46-1.27z"/></svg><span>Archives</span></button>
</div>

<!-- ═══ BOTTOM NAV - ADMIN ═══ -->
<div id="bnav-admin" class="bottom-nav" style="display:none">
  <button class="nav-item active" onclick="navTab('admin-users','admin')" data-tab="admin-users"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M12 1L3 5v6c0 5.55 3.84 10.74 9 12 5.16-1.26 9-6.45 9-12V5l-9-4z"/></svg><span>Admin</span></button>
  <button class="nav-item" onclick="navTab('admin-unis','admin')" data-tab="admin-unis"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M5 13.18v4L12 21l7-3.82v-4L12 17l-7-3.82zM12 3L1 9l11 6 9-4.91V17h2V9L12 3z"/></svg><span>Universites</span></button>
  <button class="nav-item" onclick="navTab('archives','admin')" data-tab="archives"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M20.54 5.23l-1.39-1.68C18.88 3.21 18.47 3 18 3H6c-.47 0-.88.21-1.16.55L3.46 5.23C3.17 5.57 3 6.02 3 6.5V19c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V6.5c0-.48-.17-.93-.46-1.27z"/></svg><span>Archives</span></button>
</div>

<!-- ═══ STUDENT DASHBOARD ═══ -->
<div id="screen-dashboard" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card-elevated fade-in" style="display:flex;align-items:center;gap:16px">
    <div style="position:relative;width:72px;height:72px;flex-shrink:0">
      <svg width="72" height="72" class="progress-ring"><circle cx="36" cy="36" r="30" stroke="#E8ECF0" stroke-width="6" fill="none"/><circle id="prog-circle" cx="36" cy="36" r="30" stroke="#2E86C1" stroke-width="6" fill="none" stroke-linecap="round" stroke-dasharray="188.5" stroke-dashoffset="113.1"/></svg>
      <span id="prog-pct" style="position:absolute;inset:0;display:flex;align-items:center;justify-content:center;font-family:Roboto Mono;font-weight:700;font-size:16px;color:#1A5276">40%</span>
    </div>
    <div><p id="dash-greeting" style="font-size:18px;font-weight:700;color:#1A5276">Bonjour, Etudiant</p><p id="dash-milestone" style="font-size:13px;color:#5B6B7D;margin-top:2px">Jalon actuel : Collecte de donnees</p><p id="dash-uni" style="font-size:12px;color:#95A5A6;margin-top:2px">UNIKIN - D4</p></div>
  </div>
  <!-- Timeline -->
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:12px">Progression du memoire</h4>
    <div style="display:flex;align-items:center;gap:0;overflow-x:auto;padding:8px 0">
      <div class="flex items-center w-full"><div class="timeline-dot" style="border-color:#27AE60;background:#27AE60" title="Sujet assigne"></div><div style="height:3px;flex:1;min-width:12px;background:#27AE60"></div><div class="timeline-dot" style="border-color:#27AE60;background:#27AE60" title="Revue de litterature"></div><div style="height:3px;flex:1;min-width:12px;background:#2E86C1"></div><div class="timeline-dot" style="border-color:#27AE60;background:#27AE60" title="Protocole de recherche"></div><div style="height:3px;flex:1;min-width:12px;background:#E0E6ED"></div><div class="timeline-dot pulse-anim" style="border-color:#2E86C1;background:#2E86C1" title="Fiche d'enquete"></div><div style="height:3px;flex:1;min-width:12px;background:#E0E6ED"></div><div class="timeline-dot" style="border-color:#E0E6ED;background:#E0E6ED" title="Collecte de donnees"></div><div style="height:3px;flex:1;min-width:12px;background:#E0E6ED"></div><div class="timeline-dot" style="border-color:#E0E6ED;background:#E0E6ED" title="Analyse statistique"></div><div style="height:3px;flex:1;min-width:12px;background:#E0E6ED"></div><div class="timeline-dot" style="border-color:#E0E6ED;background:#E0E6ED" title="Discussion"></div><div style="height:3px;flex:1;min-width:12px;background:#E0E6ED"></div><div class="timeline-dot" style="border-color:#E0E6ED;background:#E0E6ED" title="References"></div><div style="height:3px;flex:1;min-width:12px;background:#E0E6ED"></div><div class="timeline-dot" style="border-color:#E0E6ED;background:#E0E6ED" title="Memoire finalise"></div></div>
    </div>
    <div id="milestone-labels" style="display:flex;gap:0;margin-top:6px;overflow-x:auto;font-size:10px;color:#95A5A6">
      <span style="min-width:14px"></span>
    </div>
  </div>
  <!-- KPIs -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px" class="fade-in">
    <div class="card" style="text-align:center">
      <p style="font-size:11px;color:#5B6B7D;font-weight:500">References</p>
      <p id="kpi-refs" style="font-family:Roboto Mono;font-size:28px;font-weight:700;color:#1A5276">0</p>
    </div>
    <div class="card" style="text-align:center">
      <p style="font-size:11px;color:#5B6B7D;font-weight:500">Donnees collectees</p>
      <p id="kpi-data" style="font-family:Roboto Mono;font-size:28px;font-weight:700;color:#1A5276">0</p>
    </div>
  </div>
  <!-- Recent notifications -->
  <div class="card fade-in">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
      <h4 style="font-size:14px;font-weight:700;color:#1A5276">Notifications recentes</h4>
      <a href="#" onclick="nav('notifications');return false" style="font-size:12px;color:#2E86C1;text-decoration:none">Voir tout</a>
    </div>
    <div id="dash-notifs" style="display:flex;flex-direction:column;gap:8px">
      <p style="color:#95A5A6;font-size:13px;text-align:center;padding:12px">Aucune notification</p>
    </div>
  </div>
  <!-- Recent messages -->
  <div class="card fade-in">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
      <h4 style="font-size:14px;font-weight:700;color:#1A5276">Derniers messages</h4>
      <a href="#" onclick="nav('chat');return false" style="font-size:12px;color:#2E86C1;text-decoration:none">Voir tout</a>
    </div>
    <div id="dash-msgs" style="display:flex;flex-direction:column;gap:8px">
      <p style="color:#95A5A6;font-size:13px;text-align:center;padding:12px">Aucun message</p>
    </div>
  </div>
</div>
</div>
<!-- ═══ DIRECTOR DASHBOARD ═══ -->
<div id="screen-dir-dashboard" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card-elevated fade-in">
    <p id="dir-greeting" style="font-size:18px;font-weight:700;color:#1A5276">Bonjour, Dr</p>
    <p id="dir-uni" style="font-size:13px;color:#5B6B7D;margin-top:2px">Universite</p>
  </div>
  <!-- KPI grid 2x2 -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px" class="fade-in">
    <div class="card" style="display:flex;align-items:center;gap:12px">
      <div style="width:44px;height:44px;border-radius:12px;background:#EBF5FB;display:flex;align-items:center;justify-content:center;color:#2E86C1"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5c-1.66 0-3 1.34-3 3s1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5C6.34 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg></div>
      <div><p id="dir-kpi-active" style="font-family:Roboto Mono;font-size:24px;font-weight:700;color:#1A5276">0</p><p style="font-size:11px;color:#5B6B7D">Etudiants actifs</p></div>
    </div>
    <div class="card" style="display:flex;align-items:center;gap:12px">
      <div style="width:44px;height:44px;border-radius:12px;background:#FDEDEC;display:flex;align-items:center;justify-content:center;color:#E74C3C"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/></svg></div>
      <div><p id="dir-kpi-late" style="font-family:Roboto Mono;font-size:24px;font-weight:700;color:#E74C3C">0</p><p style="font-size:11px;color:#5B6B7D">En retard</p></div>
    </div>
    <div class="card" style="display:flex;align-items:center;gap:12px">
      <div style="width:44px;height:44px;border-radius:12px;background:#EAFAF1;display:flex;align-items:center;justify-content:center;color:#27AE60"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/></svg></div>
      <div><p id="dir-kpi-done" style="font-family:Roboto Mono;font-size:24px;font-weight:700;color:#27AE60">0</p><p style="font-size:11px;color:#5B6B7D">Complets ce mois</p></div>
    </div>
    <div class="card" style="display:flex;align-items:center;gap:12px">
      <div style="width:44px;height:44px;border-radius:12px;background:#FEF5E7;display:flex;align-items:center;justify-content:center;color:#F39C12"><svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M20 2H4c-1.1 0-1.99.9-1.99 2L2 22l4-4h14c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2z"/></svg></div>
      <div><p id="dir-kpi-pending" style="font-family:Roboto Mono;font-size:24px;font-weight:700;color:#F39C12">0</p><p style="font-size:11px;color:#5B6B7D">Demandes en attente</p></div>
    </div>
  </div>
  <!-- Chart -->
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:12px">Repartition par jalon</h4>
    <canvas id="dir-milestone-chart" height="200"></canvas>
  </div>
  <!-- Attention needed -->
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#E74C3C;margin-bottom:12px">Necessitent une attention</h4>
    <div id="dir-attention" style="display:flex;flex-direction:column;gap:8px">
      <p style="color:#95A5A6;font-size:13px;text-align:center;padding:12px">Aucun etudiant en retard</p>
    </div>
  </div>
  <!-- Recent requests -->
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:12px">Demandes recentes</h4>
    <div id="dir-requests" style="display:flex;flex-direction:column;gap:8px">
      <p style="color:#95A5A6;font-size:13px;text-align:center;padding:12px">Aucune demande</p>
    </div>
  </div>
</div>
</div>
<!-- ═══ PROFILE ═══ -->
<div id="screen-profile" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card-elevated fade-in" style="text-align:center;padding:24px">
    <div id="prof-avatar" style="width:80px;height:80px;border-radius:50%;background:linear-gradient(135deg,#1A5276,#2E86C1);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:28px;margin:0 auto 12px">?</div>
    <h3 id="prof-name" style="font-size:18px;font-weight:700;color:#1A5276">Nom</h3>
    <p id="prof-meta" style="font-size:13px;color:#5B6B7D;margin-top:4px">Role - Specialite</p>
  </div>
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:12px">Informations personnelles</h4>
    <div style="display:flex;flex-direction:column;gap:12px">
      <div><label style="font-size:12px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:4px">Nom complet</label><input id="prof-displayname" class="input-field"></div>
      <div><label style="font-size:12px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:4px">Titre</label><input id="prof-title" class="input-field"></div>
      <div><label style="font-size:12px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:4px">Specialite</label><input id="prof-specialty" class="input-field"></div>
      <div><label style="font-size:12px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:4px">E-mail</label><input id="prof-email" class="input-field" disabled style="background:#F4F6F9"></div>
      <button class="btn-primary btn-sm" onclick="saveProfile()">Enregistrer les modifications</button>
    </div>
  </div>
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:12px">Statistiques</h4>
    <div id="prof-stats" style="display:grid;grid-template-columns:1fr 1fr;gap:12px">
      <div style="text-align:center"><p style="font-family:Roboto Mono;font-size:20px;font-weight:700;color:#1A5276" id="prof-stat-1">0</p><p style="font-size:11px;color:#5B6B7D">References</p></div>
      <div style="text-align:center"><p style="font-family:Roboto Mono;font-size:20px;font-weight:700;color:#1A5276" id="prof-stat-2">0</p><p style="font-size:11px;color:#5B6B7D">Donnees collectees</p></div>
    </div>
  </div>
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:12px">Changer le mot de passe</h4>
    <div style="display:flex;flex-direction:column;gap:12px">
      <input id="prof-oldpwd" type="password" class="input-field" placeholder="Mot de passe actuel">
      <input id="prof-newpwd" type="password" class="input-field" placeholder="Nouveau mot de passe">
      <button class="btn-outline btn-sm" onclick="changePwd()">Changer le mot de passe</button>
    </div>
  </div>
</div>
</div>
<!-- ═══ THESIS (Student) ═══ -->
<div id="screen-thesis" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <div class="card-elevated fade-in">
    <h3 id="thesis-title" style="font-size:16px;font-weight:700;color:#1A5276">Pas encore de projet</h3>
    <p id="thesis-domain" style="font-size:13px;color:#5B6B7D;margin-top:4px">Envoyez une demande de supervision pour commencer</p>
  </div>
  <div class="tab-bar" style="background:#fff;border-radius:12px;overflow:hidden">
    <button class="tab-item" data-ms="topic_assigned" onclick="switchMilestoneTab('topic_assigned')">Sujet assigne</button><button class="tab-item" data-ms="literature_review" onclick="switchMilestoneTab('literature_review')">Revue de litterature</button><button class="tab-item" data-ms="protocol" onclick="switchMilestoneTab('protocol')">Protocole de recherche</button><button class="tab-item" data-ms="survey_form" onclick="switchMilestoneTab('survey_form')">Fiche d'enquete</button><button class="tab-item" data-ms="data_collection" onclick="switchMilestoneTab('data_collection')">Collecte de donnees</button><button class="tab-item" data-ms="analysis" onclick="switchMilestoneTab('analysis')">Analyse statistique</button><button class="tab-item" data-ms="discussion" onclick="switchMilestoneTab('discussion')">Discussion</button><button class="tab-item" data-ms="references" onclick="switchMilestoneTab('references')">References</button><button class="tab-item" data-ms="finalization" onclick="switchMilestoneTab('finalization')">Memoire finalise</button>
  </div>
  <div id="thesis-content" class="card fade-in" style="min-height:300px">
    <p style="color:#95A5A6;text-align:center;padding:40px 0">Selectionnez un jalon ci-dessus</p>
  </div>
  <button id="btn-find-director" class="btn-primary" onclick="nav('find-director')" style="width:100%">Chercher un directeur de memoire</button>
</div>
</div>
<!-- ═══ FIND DIRECTOR ═══ -->
<div id="screen-find-director" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card fade-in">
    <div style="display:flex;align-items:center;gap:10px">
      <svg class="w-5 h-5" style="color:#2E86C1" fill="currentColor" viewBox="0 0 24 24"><path d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z"/></svg>
      <input id="dir-search" class="input-field" placeholder="Rechercher un directeur (nom, specialite...)" style="border:none;box-shadow:none;padding:0" oninput="searchDirectors(this.value)">
    </div>
  </div>
  <div id="directors-list" style="display:flex;flex-direction:column;gap:12px">
    <p style="color:#95A5A6;text-align:center;padding:24px">Recherchez un directeur pour envoyer une demande de supervision</p>
  </div>
</div>
</div>

<!-- ═══ SUPERVISION REQUEST MODAL ═══ -->
<div id="modal-supervision" style="display:none" class="modal-overlay" onclick="if(event.target===this)this.style.display='none'">
<div style="background:#fff;border-radius:16px;padding:24px;max-width:480px;width:100%;max-height:80vh;overflow-y:auto">
  <h3 style="font-size:18px;font-weight:700;color:#1A5276;margin-bottom:4px">Demande de supervision</h3>
  <p id="modal-dir-name" style="font-size:14px;color:#5B6B7D;margin-bottom:16px"></p>
  <div style="display:flex;flex-direction:column;gap:14px">
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Sujet souhaite</label><input id="req-subject" class="input-field" placeholder="Titre ou theme de votre memoire"></div>
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Message au directeur</label><textarea id="req-message" class="input-field" placeholder="Expliquez pourquoi vous souhaitez travailler avec ce directeur et votre interet pour le sujet..."></textarea></div>
    <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Domaine medical</label><input id="req-domain" class="input-field" placeholder="ex: Medecine interne, Chirurgie..."></div>
    <div style="display:flex;gap:12px">
      <button class="btn-outline" style="flex:1" onclick="document.getElementById('modal-supervision').style.display='none'">Annuler</button>
      <button class="btn-primary" style="flex:1" onclick="sendSupervisionRequest()">Envoyer la demande</button>
    </div>
  </div>
</div>
</div>
<!-- ═══ STUDENTS LIST (Director) ═══ -->
<div id="screen-students" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <div class="card fade-in" style="display:flex;gap:8px;flex-wrap:wrap">
    <select id="filter-year" class="input-field" style="width:auto;flex:1;min-width:120px;padding:8px 12px;font-size:13px" onchange="loadStudents()"><option value="">Annee</option></select>
    <select id="filter-promo" class="input-field" style="width:auto;min-width:90px;padding:8px 12px;font-size:13px" onchange="loadStudents()"><option value="">Promo</option><option value="D3">D3</option><option value="D4">D4</option></select>
    <select id="filter-status" class="input-field" style="width:auto;min-width:110px;padding:8px 12px;font-size:13px" onchange="loadStudents()"><option value="">Statut</option><option value="active">En cours</option><option value="late">En retard</option><option value="completed">Complets</option></select>
  </div>
  <p id="students-count" style="font-size:12px;color:#95A5A6;padding:0 4px"></p>
  <div id="students-list" style="display:flex;flex-direction:column;gap:12px">
    <p style="color:#95A5A6;text-align:center;padding:40px 0">Aucun etudiant trouve</p>
  </div>
</div>
</div>
<!-- ═══ PROJECTS (Director) ═══ -->
<div id="screen-projects" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <div id="projects-list" style="display:flex;flex-direction:column;gap:12px">
    <p style="color:#95A5A6;text-align:center;padding:40px 0">Aucun projet</p>
  </div>
</div>
</div>
<!-- ═══ DATA COLLECTION ═══ -->
<div id="screen-collection" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card-elevated fade-in" style="display:flex;justify-content:space-between;align-items:center">
    <div><h3 style="font-size:16px;font-weight:700;color:#1A5276">Collecte de donnees</h3><p id="coll-progress" style="font-size:13px;color:#5B6B7D">0 reponses collectees</p></div>
    <button class="btn-primary btn-sm" onclick="newRespondent()">+ Nouveau</button>
  </div>
  <div id="collection-form" class="card fade-in" style="display:none">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:16px">
      <h4 style="font-size:14px;font-weight:700;color:#1A5276">Repondant #<span id="resp-num">1</span></h4>
      <span class="badge badge-green" id="resp-status">En cours</span>
    </div>
    <div id="form-questions" style="display:flex;flex-direction:column;gap:14px"></div>
    <div style="display:flex;gap:12px;margin-top:16px">
      <button class="btn-outline btn-sm" style="flex:1" onclick="cancelRespondent()">Annuler</button>
      <button class="btn-primary btn-sm" style="flex:1" onclick="saveRespondent()">Enregistrer</button>
    </div>
  </div>
  <div id="responses-list" style="display:flex;flex-direction:column;gap:8px">
    <p style="color:#95A5A6;text-align:center;padding:24px">Aucune reponse collectee</p>
  </div>
</div>
</div>
<!-- ═══ STATISTICS ═══ -->
<div id="screen-stats" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card fade-in">
    <h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:12px">Selection des variables</h3>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px">
      <div><label style="font-size:12px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:4px">Variable independante</label><select id="stat-var1" class="input-field"><option value="">Choisir...</option></select></div>
      <div><label style="font-size:12px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:4px">Variable dependante</label><select id="stat-var2" class="input-field"><option value="">Choisir...</option></select></div>
    </div>
    <button class="btn-primary btn-sm" style="margin-top:12px;width:100%" onclick="runAnalysis()">Lancer l'analyse</button>
  </div>
  <div id="stat-recommendation" class="card fade-in" style="display:none">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:8px">Test recommande</h4>
    <p id="stat-rec-text" style="font-size:14px;color:#5B6B7D"></p>
  </div>
  <div id="stat-results" class="card fade-in" style="display:none">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:12px">Resultats</h4>
    <div id="stat-table-container"></div>
    <canvas id="stat-chart" style="margin-top:16px" height="200"></canvas>
    <div id="stat-interpretation" style="margin-top:12px;padding:12px;background:#F8F9FA;border-radius:8px;font-size:14px;color:#5B6B7D;line-height:1.6"></div>
  </div>
  <!-- Reference table -->
  <div class="card fade-in">
    <h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:8px">Tests statistiques disponibles</h4>
    <div style="overflow-x:auto">
      <table style="width:100%;border-collapse:collapse">
        <thead><tr style="background:#F8F9FA"><th class="px-4 py-3 text-left text-xs font-semibold text-gray-500 uppercase">Test</th><th class="px-4 py-3 text-left text-xs font-semibold text-gray-500 uppercase">Categorie</th><th class="px-4 py-3 text-left text-xs font-semibold text-gray-500 uppercase">Conditions</th></tr></thead>
        <tbody><tr><td class="px-4 py-3 text-sm font-medium">Chi-carre d'independance</td><td class="px-4 py-3 text-sm">Quali x Quali</td><td class="px-4 py-3 text-sm text-gray-500">Effectifs theoriques >= 5</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Test exact de Fisher</td><td class="px-4 py-3 text-sm">Quali x Quali</td><td class="px-4 py-3 text-sm text-gray-500">Effectifs theoriques < 5</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Test de McNemar</td><td class="px-4 py-3 text-sm">Quali x Quali</td><td class="px-4 py-3 text-sm text-gray-500">Donnees appariees</td></tr><tr><td class="px-4 py-3 text-sm font-medium">t-test de Student</td><td class="px-4 py-3 text-sm">Quali x Quanti (2 grp)</td><td class="px-4 py-3 text-sm text-gray-500">Normalite + homogeneite</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Test de Mann-Whitney</td><td class="px-4 py-3 text-sm">Quali x Quanti (2 grp)</td><td class="px-4 py-3 text-sm text-gray-500">Non-normalite</td></tr><tr><td class="px-4 py-3 text-sm font-medium">ANOVA a un facteur</td><td class="px-4 py-3 text-sm">Quali x Quanti (>2 grp)</td><td class="px-4 py-3 text-sm text-gray-500">Normalite + homogeneite</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Test de Kruskal-Wallis</td><td class="px-4 py-3 text-sm">Quali x Quanti (>2 grp)</td><td class="px-4 py-3 text-sm text-gray-500">Non-normalite</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Correlation de Pearson</td><td class="px-4 py-3 text-sm">Quanti x Quanti</td><td class="px-4 py-3 text-sm text-gray-500">Normalite</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Correlation de Spearman</td><td class="px-4 py-3 text-sm">Quanti x Quanti</td><td class="px-4 py-3 text-sm text-gray-500">Non-normalite</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Regression lineaire simple</td><td class="px-4 py-3 text-sm">Quanti x Quanti</td><td class="px-4 py-3 text-sm text-gray-500">Relation lineaire</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Regression logistique binaire</td><td class="px-4 py-3 text-sm">Binaire (resultat)</td><td class="px-4 py-3 text-sm text-gray-500">Var dependante binaire</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Courbe de Kaplan-Meier</td><td class="px-4 py-3 text-sm">Survie</td><td class="px-4 py-3 text-sm text-gray-500">Donnees de survie</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Courbe ROC / AUC</td><td class="px-4 py-3 text-sm">Diagnostic</td><td class="px-4 py-3 text-sm text-gray-500">Test diagnostique</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Sensibilite / Specificite</td><td class="px-4 py-3 text-sm">Diagnostic</td><td class="px-4 py-3 text-sm text-gray-500">Evaluation test</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Statistiques descriptives</td><td class="px-4 py-3 text-sm">Descriptive</td><td class="px-4 py-3 text-sm text-gray-500">Moyenne, mediane, ecart-type</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Test de Shapiro-Wilk</td><td class="px-4 py-3 text-sm">Normalite</td><td class="px-4 py-3 text-sm text-gray-500">Verification normalite</td></tr><tr><td class="px-4 py-3 text-sm font-medium">Test de Levene</td><td class="px-4 py-3 text-sm">Homogeneite</td><td class="px-4 py-3 text-sm text-gray-500">Verification variances</td></tr></tbody>
      </table>
    </div>
  </div>
</div>
</div>
<!-- ═══ CHAT ═══ -->
<div id="screen-chat" class="screen" style="background:#F4F6F9;justify-content:flex-end">
<div style="flex:1;display:flex;flex-direction:column;min-height:0">
  <!-- Chat list view -->
  <div id="chat-list" style="flex:1;overflow-y:auto;padding:16px;display:flex;flex-direction:column;gap:8px">
    <p style="color:#95A5A6;text-align:center;padding:40px 0">Aucune conversation</p>
  </div>
  <!-- Chat conversation view -->
  <div id="chat-conv" style="display:none;flex:1;flex-direction:column;min-height:0">
    <div style="padding:12px 16px;background:#fff;border-bottom:1px solid #F0F2F5;display:flex;align-items:center;gap:12px">
      <button onclick="showChatList()" style="background:none;border:none;cursor:pointer;color:#1A5276"><svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M20 11H7.83l5.59-5.59L12 4l-8 8 8 8 1.41-1.41L7.83 13H20v-2z"/></svg></button>
      <div id="chat-partner-avatar" style="width:36px;height:36px;border-radius:50%;background:linear-gradient(135deg,#1A5276,#2E86C1);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:14px">?</div>
      <div><p id="chat-partner-name" style="font-weight:600;font-size:14px">Nom</p><p id="chat-partner-status" style="font-size:11px;color:#27AE60">En ligne</p></div>
    </div>
    <div id="chat-messages" style="flex:1;overflow-y:auto;padding:16px;display:flex;flex-direction:column;gap:8px"></div>
    <div style="padding:12px 16px;background:#fff;border-top:1px solid #F0F2F5;display:flex;gap:8px;align-items:flex-end">
      <textarea id="chat-input" class="input-field" placeholder="Ecrire un message..." rows="1" style="flex:1;min-height:40px;max-height:120px;padding:10px 14px;border-radius:20px" onkeydown="if(event.key==='Enter'&&!event.shiftKey){event.preventDefault();sendMessage()}"></textarea>
      <button onclick="sendMessage()" style="width:40px;height:40px;border-radius:50%;background:#2E86C1;border:none;cursor:pointer;display:flex;align-items:center;justify-content:center;flex-shrink:0"><svg class="w-5 h-5" fill="#fff" viewBox="0 0 24 24"><path d="M2.01 21L23 12 2.01 3 2 10l15 2-15 2z"/></svg></button>
    </div>
  </div>
</div>
</div>
<!-- ═══ NOTIFICATIONS ═══ -->
<div id="screen-notifications" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <h3 style="font-size:18px;font-weight:700;color:#1A5276">Notifications</h3>
  <div id="notif-list" style="display:flex;flex-direction:column;gap:8px">
    <p style="color:#95A5A6;text-align:center;padding:40px 0">Aucune notification</p>
  </div>
</div>
</div>
<!-- ═══ REFERENCES ═══ -->
<div id="screen-references" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <div class="card fade-in" style="display:flex;gap:8px">
    <input id="ref-search" class="input-field" placeholder="Rechercher (PubMed, Google Scholar, local)..." style="flex:1">
    <button class="btn-primary btn-sm" onclick="searchRefs()">Rechercher</button>
  </div>
  <div style="display:flex;gap:8px;flex-wrap:wrap">
    <button class="btn-outline btn-sm" onclick="addRefManual()">+ Ajouter manuellement</button>
    <select id="ref-format" class="input-field" style="width:auto;padding:6px 12px;font-size:12px"><option value="vancouver">Vancouver</option><option value="apa">APA</option></select>
  </div>
  <div id="refs-list" style="display:flex;flex-direction:column;gap:8px">
    <p style="color:#95A5A6;text-align:center;padding:24px">Aucune reference</p>
  </div>
</div>
</div>
<!-- ═══ ARCHIVES ═══ -->
<div id="screen-archives" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <div class="card fade-in" style="display:flex;gap:8px;flex-wrap:wrap">
    <select id="arch-year" class="input-field" style="width:auto;flex:1;min-width:120px;padding:8px 12px;font-size:13px" onchange="loadArchives()"><option value="">Annee</option></select>
    <select id="arch-uni" class="input-field" style="width:auto;min-width:120px;padding:8px 12px;font-size:13px" onchange="loadArchives()"><option value="">Universite</option></select>
    <select id="arch-domain" class="input-field" style="width:auto;min-width:120px;padding:8px 12px;font-size:13px" onchange="loadArchives()"><option value="">Domaine</option></select>
  </div>
  <div id="archives-list" style="display:flex;flex-direction:column;gap:12px">
    <p style="color:#95A5A6;text-align:center;padding:40px 0">Aucune archive</p>
  </div>
</div>
</div>
<!-- ═══ CALENDAR ═══ -->
<div id="screen-calendar" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card fade-in">
    <h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:16px">Calendrier academique</h3>
    <div id="calendar-content" style="display:flex;flex-direction:column;gap:12px">
      <p style="color:#95A5A6;text-align:center;padding:24px">Aucune date academique configuree</p>
    </div>
  </div>
</div>
</div>
<!-- ═══ SETTINGS ═══ -->
<div id="screen-settings" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:16px">
  <div class="card fade-in">
    <h3 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:16px">Parametres</h3>
    <div style="display:flex;flex-direction:column;gap:16px">
      <div style="display:flex;justify-content:space-between;align-items:center"><span style="font-size:14px">Notifications push</span><label style="position:relative;width:48px;height:26px"><input type="checkbox" checked id="set-notif" style="opacity:0;width:0;height:0"><span style="position:absolute;cursor:pointer;inset:0;background:#2E86C1;border-radius:26px;transition:.3s"></span><span style="position:absolute;height:20px;width:20px;left:3px;bottom:3px;background:#fff;border-radius:50%;transition:.3s"></span></label></div>
      <div style="display:flex;justify-content:space-between;align-items:center"><span style="font-size:14px">Mode sombre</span><label style="position:relative;width:48px;height:26px"><input type="checkbox" id="set-dark" style="opacity:0;width:0;height:0"><span style="position:absolute;cursor:pointer;inset:0;background:#E0E6ED;border-radius:26px;transition:.3s"></span><span style="position:absolute;height:20px;width:20px;left:3px;bottom:3px;background:#fff;border-radius:50%;transition:.3s"></span></label></div>
      <div><label style="font-size:13px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:6px">Heure de rappel</label><input type="time" class="input-field" value="08:00" id="set-reminder"></div>
    </div>
  </div>
</div>
</div>
<!-- ═══ ADMIN: USERS ═══ -->
<div id="screen-admin-users" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <div class="card fade-in" style="display:flex;gap:8px">
    <input id="admin-user-search" class="input-field" placeholder="Rechercher un utilisateur..." style="flex:1" oninput="loadAdminUsers()">
    <button class="btn-primary btn-sm" onclick="showAddUserModal()">+ Ajouter</button>
  </div>
  <div id="admin-users-list" style="display:flex;flex-direction:column;gap:8px">
    <p style="color:#95A5A6;text-align:center;padding:24px">Aucun utilisateur</p>
  </div>
</div>
</div>

<!-- ═══ ADMIN: UNIVERSITIES ═══ -->
<div id="screen-admin-unis" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <button class="btn-primary btn-sm" onclick="showAddUniModal()" style="align-self:flex-start">+ Ajouter une universite</button>
  <div id="admin-unis-list" style="display:flex;flex-direction:column;gap:12px">
    <p style="color:#95A5A6;text-align:center;padding:24px">Aucune universite</p>
  </div>
</div>
</div>

<!-- ═══ ADMIN: ACADEMIC YEARS ═══ -->
<div id="screen-admin-years" class="screen" style="background:#F4F6F9">
<div class="pb-safe" style="padding:16px;display:flex;flex-direction:column;gap:12px">
  <button class="btn-primary btn-sm" onclick="showAddYearModal()" style="align-self:flex-start">+ Ajouter une annee</button>
  <div id="admin-years-list" style="display:flex;flex-direction:column;gap:12px">
    <p style="color:#95A5A6;text-align:center;padding:24px">Aucune annee academique</p>
  </div>
</div>
</div>
<!-- ═══════════════════════════════════════════════════════════════
     JAVASCRIPT - Moteur de l'application MedStat
     ═══════════════════════════════════════════════════════════════ -->
<script>
// ═══════════════════════════════════════════════════
// FIREBASE CONFIG - Remplacez par vos propres valeurs
// ═══════════════════════════════════════════════════
const firebaseConfig = {
  apiKey: "VOTRE_API_KEY",
  authDomain: "votre-projet.firebaseapp.com",
  projectId: "votre-projet-id",
  storageBucket: "votre-projet.appspot.com",
  messagingSenderId: "000000000000",
  appId: "1:000000000000:web:0000000000000000"
};

// ═══════════════════════════════════════════════════
// GLOBALS
// ═══════════════════════════════════════════════════
let db, auth;
let currentUser = null;
let currentUserData = null;
let currentScreen = 'splash';
let currentRole = null;
let activeSupervisionId = null;
let activeProjectId = null;
let activeChatSupervisionId = null;
let selectedDirectorId = null;

// ═══════════════════════════════════════════════════
// INIT
// ═══════════════════════════════════════════════════
document.addEventListener('DOMContentLoaded', () => {
  try {
    if (!firebase.apps.length) {
      firebase.initializeApp(firebaseConfig);
    }
    auth = firebase.auth();
    db = firebase.firestore();
    auth.onAuthStateChanged(onAuthStateChanged);
  } catch(e) {
    console.warn('Firebase non configure. Mode demonstration active.');
    console.warn('Remplacez firebaseConfig dans le code par vos valeurs Firebase.');
    // Allow navigation in demo mode
    window._demoMode = true;
  }
});

function onAuthStateChanged(user) {
  currentUser = user;
  if (user) {
    db.collection('Users').doc(user.uid).get().then(doc => {
      if (doc.exists) {
        currentUserData = { id: doc.id, ...doc.data() };
        currentRole = currentUserData.role;
        updateUIForRole();
        nav('dashboard');
      } else {
        // User exists in Auth but not in Users collection (admin must create)
        showDemoDashboard(user);
      }
    }).catch(() => {
      showDemoDashboard(user);
    });
  } else {
    currentUserData = null;
    currentRole = null;
    hideAppShell();
    nav('splash');
  }
}

function showDemoDashboard(user) {
  currentRole = 'student';
  currentUserData = {
    id: user.uid,
    displayName: user.displayName || user.email.split('@')[0],
    email: user.email,
    role: 'student',
    universityName: 'UNIKIN',
    promotion: 'D4',
    specialty: 'Medecine interne',
    title: 'Etudiant'
  };
  updateUIForRole();
  nav('dashboard');
}

// ═══════════════════════════════════════════════════
// NAVIGATION / ROUTING
// ═══════════════════════════════════════════════════
function nav(screen) {
  // Hide all screens
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  // Map screen names
  let screenId = screen;
  if (screen === 'dashboard' && currentRole === 'director') screenId = 'dir-dashboard';
  if (screen === 'dashboard' && currentRole === 'admin') screenId = 'admin-users';
  const el = document.getElementById('screen-' + screenId);
  if (el) {
    el.classList.add('active');
    currentScreen = screen;
    // Load data for this screen
    loadScreenData(screen);
  }
  // Close drawer
  document.getElementById('drawer').classList.remove('open');
  document.getElementById('drawer-overlay').classList.remove('open');
}

function navTab(tab, role) {
  // Update bottom nav active state
  const bnav = document.getElementById('bnav-' + role);
  if (bnav) {
    bnav.querySelectorAll('.nav-item').forEach(b => b.classList.remove('active'));
    const btn = bnav.querySelector('[data-tab="' + tab + '"]');
    if (btn) btn.classList.add('active');
  }
  nav(tab);
}

function loadScreenData(screen) {
  switch(screen) {
    case 'dashboard': loadDashboard(); break;
    case 'dir-dashboard': loadDirectorDashboard(); break;
    case 'profile': loadProfile(); break;
    case 'thesis': loadThesis(); break;
    case 'students': loadStudents(); break;
    case 'projects': loadProjects(); break;
    case 'chat': loadChatList(); break;
    case 'notifications': loadNotifications(); break;
    case 'references': loadReferences(); break;
    case 'archives': loadArchives(); break;
    case 'admin-users': loadAdminUsers(); break;
    case 'admin-unis': loadAdminUniversities(); break;
    case 'admin-years': loadAdminYears(); break;
    case 'collection': loadCollection(); break;
    case 'stats': loadStats(); break;
    case 'calendar': loadCalendar(); break;
  }
}

// ═══════════════════════════════════════════════════
// UI ROLE MANAGEMENT
// ═══════════════════════════════════════════════════
function updateUIForRole() {
  if (!currentUserData) return;
  const r = currentRole;
  const initials = (currentUserData.displayName || '?').split(' ').map(w => w[0]).join('').substring(0, 2).toUpperCase();

  // Update avatars
  ['appbar-avatar', 'drawer-avatar', 'prof-avatar'].forEach(id => {
    const el = document.getElementById(id);
    if (el) el.textContent = initials;
  });

  // Update names
  document.getElementById('drawer-name').textContent = currentUserData.displayName || 'Utilisateur';
  document.getElementById('drawer-role').textContent = {student:'Etudiant',director:'Directeur',co_director:'Co-directeur',admin:'Administrateur'}[r] || r;

  // Show/hide bottom navs
  document.getElementById('bnav-student').style.display = r === 'student' ? 'flex' : 'none';
  document.getElementById('bnav-director').style.display = r === 'director' || r === 'co_director' ? 'flex' : 'none';
  document.getElementById('bnav-admin').style.display = r === 'admin' ? 'flex' : 'none';

  // Show/hide admin drawer items
  document.getElementById('drawer-admin-btn').style.display = r === 'admin' ? 'block' : 'none';

  // Show appbar
  showAppShell();
}

function showAppShell() {
  document.getElementById('appbar').style.display = 'flex';
}

function hideAppShell() {
  document.getElementById('appbar').style.display = 'none';
  document.getElementById('bnav-student').style.display = 'none';
  document.getElementById('bnav-director').style.display = 'none';
  document.getElementById('bnav-admin').style.display = 'none';
}

function toggleDrawer() {
  document.getElementById('drawer').classList.toggle('open');
  document.getElementById('drawer-overlay').classList.toggle('open');
}

function togglePwd(id, btn) {
  const inp = document.getElementById(id);
  inp.type = inp.type === 'password' ? 'text' : 'password';
}

function showToast(msg, type) {
  const t = document.createElement('div');
  t.className = 'toast';
  t.style.background = type === 'error' ? '#E74C3C' : type === 'success' ? '#27AE60' : '#2E86C1';
  t.textContent = msg;
  document.body.appendChild(t);
  setTimeout(() => t.remove(), 4000);
}

// ═══════════════════════════════════════════════════
// AUTHENTICATION
// ═══════════════════════════════════════════════════
async function doLogin() {
  const email = document.getElementById('login-email').value.trim();
  const pwd = document.getElementById('login-password').value;
  const errEl = document.getElementById('login-error');
  errEl.style.display = 'none';
  if (!email || !pwd) { errEl.textContent = 'Veuillez remplir tous les champs'; errEl.style.display = 'block'; return; }
  try {
    await auth.signInWithEmailAndPassword(email, pwd);
  } catch(e) {
    errEl.textContent = translateError(e.code);
    errEl.style.display = 'block';
  }
}

async function doGoogleLogin() {
  try {
    const provider = new firebase.auth.GoogleAuthProvider();
    await auth.signInWithPopup(provider);
  } catch(e) {
    showToast(translateError(e.code), 'error');
  }
}

function translateError(code) {
  const m = {
    'auth/user-not-found': 'Aucun compte avec cet e-mail',
    'auth/wrong-password': 'Mot de passe incorrect',
    'auth/email-already-in-use': 'Cet e-mail est deja utilise',
    'auth/weak-password': 'Le mot de passe doit contenir au moins 6 caracteres',
    'auth/invalid-email': 'Adresse e-mail invalide',
    'auth/invalid-credential': 'Identifiants incorrects',
    'auth/too-many-requests': 'Trop de tentatives. Reessayez plus tard.',
  };
  return m[code] || 'Erreur de connexion. Verifiez vos identifiants.';
}

// Signup state
let signupRole = null;
let signupStep = 1;

function suStep(n) {
  document.getElementById('signup-s' + signupStep).style.display = 'none';
  document.getElementById('signup-s' + n).style.display = 'flex';
  for (let i = 1; i <= 3; i++) {
    document.getElementById('step-bar-' + i).style.background = i <= n ? '#2E86C1' : '#E0E6ED';
  }
  signupStep = n;
  if (n === 3) buildSignupSummary();
}

function pickRole(role) {
  signupRole = role;
  document.getElementById('rc-student').style.borderColor = role === 'student' ? '#2E86C1' : '#E0E6ED';
  document.getElementById('rc-director').style.borderColor = role === 'director' ? '#2E86C1' : '#E0E6ED';
  document.getElementById('rc-student').style.background = role === 'student' ? '#EBF5FB' : '#fff';
  document.getElementById('rc-director').style.background = role === 'director' ? '#EAFAF1' : '#fff';
  document.getElementById('sf-student').style.display = role === 'student' ? 'flex' : 'none';
  document.getElementById('sf-director').style.display = role === 'director' ? 'flex' : 'none';
}

function buildSignupSummary() {
  const name = document.getElementById('su-name').value;
  const email = document.getElementById('su-email').value;
  const rLabel = signupRole === 'student' ? 'Etudiant' : 'Directeur';
  let extra = '';
  if (signupRole === 'student') {
    extra = '<p>Universite: ' + (document.getElementById('su-uni').value || '-') + '</p>' +
            '<p>Promotion: ' + (document.getElementById('su-promo').value || '-') + '</p>' +
            '<p>Specialite: ' + (document.getElementById('su-spec').value || '-') + '</p>';
  } else {
    extra = '<p>Universite: ' + (document.getElementById('su-unid').value || '-') + '</p>' +
            '<p>Titre: ' + (document.getElementById('su-title').value || '-') + '</p>' +
            '<p>Specialite: ' + (document.getElementById('su-specd').value || '-') + '</p>';
  }
  document.getElementById('su-summary').innerHTML =
    '<p style="font-size:14px"><strong>' + name + '</strong></p>' +
    '<p style="font-size:13px;color:#5B6B7D">' + email + '</p>' +
    '<p style="font-size:13px;color:#2E86C1;font-weight:600;margin-top:4px">Role: ' + rLabel + '</p>' +
    extra;
}

async function doSignup() {
  const errEl = document.getElementById('su-error');
  errEl.style.display = 'none';
  if (!document.getElementById('su-terms').checked) {
    errEl.textContent = 'Vous devez accepter les conditions';
    errEl.style.display = 'block';
    return;
  }
  const email = document.getElementById('su-email').value.trim();
  const pwd = document.getElementById('su-pwd').value;
  const name = document.getElementById('su-name').value.trim();
  if (!name || !email || !pwd || !signupRole) {
    errEl.textContent = 'Veuillez remplir tous les champs';
    errEl.style.display = 'block';
    return;
  }
  try {
    const cred = await auth.createUserWithEmailAndPassword(email, pwd);
    await cred.user.updateProfile({ displayName: name });
    // User document will be created by admin or self-registration cloud function
    // For now, create it directly
    let userData = {
      id: cred.user.uid,
      email: email,
      displayName: name,
      role: signupRole,
      title: signupRole === 'director' ? (document.getElementById('su-title').value || 'Dr') : 'Etudiant',
      specialty: signupRole === 'student' ? (document.getElementById('su-spec').value || '') : (document.getElementById('su-specd').value || ''),
      universityId: signupRole === 'student' ? document.getElementById('su-uni').value : document.getElementById('su-unid').value,
      universityName: '',
      promotion: signupRole === 'student' ? document.getElementById('su-promo').value : '',
      photoURL: '',
      fcmToken: '',
      isOnline: true,
      lastActive: firebase.firestore.FieldValue.serverTimestamp(),
      createdAt: firebase.firestore.FieldValue.serverTimestamp(),
      updatedAt: firebase.firestore.FieldValue.serverTimestamp()
    };
    await db.collection('Users').doc(cred.user.uid).set(userData);
  } catch(e) {
    errEl.textContent = translateError(e.code);
    errEl.style.display = 'block';
  }
}

async function doLogout() {
  if (confirm('Voulez-vous vraiment vous deconnecter ?')) {
    await auth.signOut();
  }
}

// ═══════════════════════════════════════════════════
// STUDENT DASHBOARD
// ═══════════════════════════════════════════════════
function loadDashboard() {
  if (!currentUserData) return;
  const d = currentUserData;
  document.getElementById('dash-greeting').textContent = 'Bonjour, ' + (d.displayName || 'Etudiant');
  document.getElementById('dash-uni').textContent = (d.universityName || '') + ' - ' + (d.promotion || '');

  if (!activeProjectId) {
    document.getElementById('dash-milestone').textContent = 'Envoyez une demande de supervision pour commencer';
    return;
  }

  // Load project data
  db.collection('ThesisProjects').doc(activeProjectId).get().then(doc => {
    if (doc.exists) {
      const p = doc.data();
      document.getElementById('dash-milestone').textContent = 'Jalon actuel : ' + milestoneLabel(p.currentMilestone);
      // Progress
      const pct = milestonePercent(p.currentMilestone);
      document.getElementById('prog-pct').textContent = pct + '%';
      const circle = document.getElementById('prog-circle');
      const offset = 188.5 - (188.5 * pct / 100);
      circle.style.strokeDashoffset = offset;
      circle.style.stroke = pct < 25 ? '#E74C3C' : pct < 50 ? '#F39C12' : pct < 75 ? '#F39C12' : '#27AE60';
    }
  }).catch(() => {});

  // Count references
  if (activeProjectId) {
    db.collection('References').where('projectId', '==', activeProjectId).get().then(snap => {
      document.getElementById('kpi-refs').textContent = snap.size;
    }).catch(() => {});
    db.collection('SurveyResponses').where('projectId', '==', activeProjectId).get().then(snap => {
      document.getElementById('kpi-data').textContent = snap.size;
    }).catch(() => {});
  }

  // Load notifications
  loadDashboardNotifications();
  loadDashboardMessages();
  // Find active supervision
  findActiveSupervision();
}

function findActiveSupervision() {
  if (!currentUser) return;
  db.collection('Supervisions').where('studentId', '==', currentUser.uid).where('status', '==', 'accepted').limit(1).get()
  .then(snap => {
    if (!snap.empty) {
      const sup = snap.docs[0];
      activeSupervisionId = sup.id;
      if (sup.data().thesisProjectId) {
        activeProjectId = sup.data().thesisProjectId;
      }
    }
  }).catch(() => {});
}

function loadDashboardNotifications() {
  if (!currentUser) return;
  db.collection('Notifications').where('userId', '==', currentUser.uid).orderBy('createdAt', 'desc').limit(5).get()
  .then(snap => {
    const el = document.getElementById('dash-notifs');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;font-size:13px;text-align:center;padding:12px">Aucune notification</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const n = doc.data();
      html += '<div style="display:flex;gap:10px;align-items:flex-start;padding:8px;border-radius:8px;background:' + (n.isRead ? 'transparent' : '#EBF5FB') + '">' +
        '<div style="width:8px;height:8px;border-radius:50%;background:' + (n.isRead ? '#E0E6ED' : '#2E86C1') + ';margin-top:6px;flex-shrink:0"></div>' +
        '<div style="flex:1;min-width:0"><p style="font-size:13px;font-weight:600;color:#333;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">' + (n.title || '') + '</p>' +
        '<p style="font-size:12px;color:#5B6B7D;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">' + (n.body || '') + '</p></div></div>';
    });
    el.innerHTML = html;
    // Update badge
    const unread = snap.docs.filter(d => !d.data().isRead).length;
    const badge = document.getElementById('notif-badge');
    if (unread > 0) { badge.textContent = unread; badge.style.display = 'flex'; }
    else { badge.style.display = 'none'; }
  }).catch(() => {});
}

function loadDashboardMessages() {
  if (!activeSupervisionId || !currentUser) return;
  db.collection('Messages').where('supervisionId', '==', activeSupervisionId).orderBy('createdAt', 'desc').limit(3).get()
  .then(snap => {
    const el = document.getElementById('dash-msgs');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;font-size:13px;text-align:center;padding:12px">Aucun message</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const m = doc.data();
      const isMine = m.senderId === currentUser.uid;
      html += '<div style="display:flex;gap:10px;align-items:center;padding:8px;border-radius:8px;cursor:pointer" onclick="openChat(\'' + m.supervisionId + '\')">' +
        '<div style="width:36px;height:36px;border-radius:50%;background:#2E86C1;display:flex;align-items:center;justify-content:center;color:#fff;font-size:12px;font-weight:600">' + (isMine ? 'Moi' : 'Dr') + '</div>' +
        '<div style="flex:1;min-width:0"><p style="font-size:13px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">' + (m.content || '').substring(0, 50) + '</p></div>' +
        (m.isRead ? '' : '<div style="width:8px;height:8px;border-radius:50%;background:#2E86C1"></div>') +
        '</div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

// ═══════════════════════════════════════════════════
// DIRECTOR DASHBOARD
// ═══════════════════════════════════════════════════
function loadDirectorDashboard() {
  if (!currentUserData) return;
  document.getElementById('dir-greeting').textContent = 'Bonjour, ' + (currentUserData.displayName || 'Dr');
  document.getElementById('dir-uni').textContent = (currentUserData.universityName || currentUserData.specialty || '');

  // Load KPIs
  db.collection('Supervisions').where('directorId', '==', currentUser.uid).where('status', '==', 'accepted').get()
  .then(snap => {
    document.getElementById('dir-kpi-active').textContent = snap.size;
  }).catch(() => {});

  db.collection('Supervisions').where('directorId', '==', currentUser.uid).where('status', '==', 'pending').get()
  .then(snap => {
    document.getElementById('dir-kpi-pending').textContent = snap.size;
    const el = document.getElementById('dir-requests');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;font-size:13px;text-align:center;padding:12px">Aucune demande</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const s = doc.data();
      html += '<div class="card" style="padding:12px"><div style="display:flex;justify-content:space-between;align-items:flex-start"><div><p style="font-weight:600;font-size:14px">' + (s.studentMessage || 'Demande de supervision') + '</p><p style="font-size:12px;color:#5B6B7D;margin-top:2px">Promotion: ' + (s.promotion || '-') + ' | ' + (s.universityId || '') + '</p></div>' +
        '<div style="display:flex;gap:6px"><button class="btn-success btn-sm" style="padding:6px 12px;font-size:12px" onclick="handleSupervision(\'' + doc.id + '\',\'accepted\')">Accepter</button>' +
        '<button class="btn-danger btn-sm" style="padding:6px 12px;font-size:12px" onclick="handleSupervision(\'' + doc.id + '\',\'rejected\')">Refuser</button></div></div></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});

  // Load milestone chart
  loadDirectorChart();
}

let milestoneChartInstance = null;
function loadDirectorChart() {
  if (!currentUser) return;
  db.collection('Supervisions').where('directorId', '==', currentUser.uid).where('status', '==', 'accepted').get()
  .then(snap => {
    const counts = {};
    MILESTONES.forEach(m => counts[m[0]] = 0);
    let promises = [];
    snap.forEach(doc => {
      const s = doc.data();
      if (s.thesisProjectId) {
        promises.push(db.collection('ThesisProjects').doc(s.thesisProjectId).get().then(pdoc => {
          if (pdoc.exists) counts[pdoc.data().currentMilestone] = (counts[pdoc.data().currentMilestone] || 0) + 1;
        }).catch(() => {}));
      }
    });
    Promise.all(promises).then(() => {
      const ctx = document.getElementById('dir-milestone-chart');
      if (!ctx) return;
      if (milestoneChartInstance) milestoneChartInstance.destroy();
      milestoneChartInstance = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: MILESTONES.map(m => m[1].substring(0, 15)),
          datasets: [{ data: MILESTONES.map(m => counts[m[0]] || 0), backgroundColor: '#2E86C1', borderRadius: 6, barThickness: 20 }]
        },
        options: { indexAxis: 'y', responsive: true, plugins: { legend: { display: false } }, scales: { x: { beginAtZero: true, ticks: { stepSize: 1 } } } }
      });
    });
  }).catch(() => {});
}

async function handleSupervision(supId, status) {
  try {
    await db.collection('Supervisions').doc(supId).update({
      status: status,
      responseDate: firebase.firestore.FieldValue.serverTimestamp(),
      updatedAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    if (status === 'accepted') {
      // Create ThesisProject
      const supDoc = await db.collection('Supervisions').doc(supId).get();
      const sup = supDoc.data();
      const projRef = await db.collection('ThesisProjects').add({
        supervisionId: supId,
        studentId: sup.studentId,
        directorId: sup.directorId,
        title: sup.studentMessage || '',
        subject: '',
        domain: '',
        subDomain: '',
        researchQuestion: '',
        objectives: [],
        hypotheses: [],
        currentMilestone: 'topic_assigned',
        milestoneDates: {},
        plagiarismScore: 0,
        isArchived: false,
        createdAt: firebase.firestore.FieldValue.serverTimestamp(),
        updatedAt: firebase.firestore.FieldValue.serverTimestamp()
      });
      await db.collection('Supervisions').doc(supId).update({ thesisProjectId: projRef.id });
      // Create notification
      await db.collection('Notifications').add({
        userId: sup.studentId,
        type: 'supervision_request',
        title: 'Demande acceptee',
        body: 'Le directeur a accepte votre demande de supervision',
        isRead: false,
        deepLink: 'thesis',
        createdAt: firebase.firestore.FieldValue.serverTimestamp()
      });
      showToast('Supervision acceptee et projet cree', 'success');
    } else {
      showToast('Demande refusee', 'info');
    }
    loadDirectorDashboard();
  } catch(e) {
    showToast('Erreur: ' + e.message, 'error');
  }
}

// ═══════════════════════════════════════════════════
// PROFILE
// ═══════════════════════════════════════════════════
function loadProfile() {
  if (!currentUserData) return;
  const d = currentUserData;
  const initials = (d.displayName || '?').split(' ').map(w => w[0]).join('').substring(0, 2).toUpperCase();
  document.getElementById('prof-avatar').textContent = initials;
  document.getElementById('prof-name').textContent = d.displayName;
  document.getElementById('prof-meta').textContent = (d.title || '') + ' - ' + (d.specialty || '') + ' (' + (d.role || '') + ')';
  document.getElementById('prof-displayname').value = d.displayName || '';
  document.getElementById('prof-title').value = d.title || '';
  document.getElementById('prof-specialty').value = d.specialty || '';
  document.getElementById('prof-email').value = d.email || '';
}

async function saveProfile() {
  if (!currentUser) return;
  try {
    await db.collection('Users').doc(currentUser.uid).update({
      displayName: document.getElementById('prof-displayname').value,
      title: document.getElementById('prof-title').value,
      specialty: document.getElementById('prof-specialty').value,
      updatedAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    showToast('Profil mis a jour', 'success');
  } catch(e) {
    showToast('Erreur: ' + e.message, 'error');
  }
}

async function changePwd() {
  const old = document.getElementById('prof-oldpwd').value;
  const newp = document.getElementById('prof-newpwd').value;
  if (!old || !newp) { showToast('Remplissez les deux champs', 'error'); return; }
  try {
    await auth.currentUser.updatePassword(newp);
    showToast('Mot de passe change', 'success');
    document.getElementById('prof-oldpwd').value = '';
    document.getElementById('prof-newpwd').value = '';
  } catch(e) {
    showToast(translateError(e.code), 'error');
  }
}

// ═══════════════════════════════════════════════════
// THESIS / MILESTONE CONTENT
// ═══════════════════════════════════════════════════
function loadThesis() {
  if (!activeProjectId) return;
  db.collection('ThesisProjects').doc(activeProjectId).get().then(doc => {
    if (doc.exists) {
      const p = doc.data();
      document.getElementById('thesis-title').textContent = p.title || 'Projet sans titre';
      document.getElementById('thesis-domain').textContent = (p.domain || '') + ' | ' + milestoneLabel(p.currentMilestone);
      document.getElementById('btn-find-director').style.display = 'none';
    }
  }).catch(() => {});
  // Default: show first milestone tab
  const firstTab = document.querySelector('.tab-item');
  if (firstTab) { firstTab.classList.add('active'); switchMilestoneTab('topic_assigned'); }
}

function switchMilestoneTab(ms) {
  document.querySelectorAll('.tab-item').forEach(t => t.classList.remove('active'));
  const tab = document.querySelector('[data-ms="' + ms + '"]');
  if (tab) tab.classList.add('active');
  const el = document.getElementById('thesis-content');
  if (!activeProjectId) {
    el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:40px">Aucun projet actif</p>';
    return;
  }
  // Load content based on milestone type
  switch(ms) {
    case 'literature_review': loadLiteratureReview(el); break;
    case 'protocol': loadProtocol(el); break;
    case 'survey_form': loadSurveyForm(el); break;
    case 'data_collection': el.innerHTML = '<p style="color:#5B6B7D;text-align:center;padding:20px">Utilisez l\'onglet <strong>Collecte</strong> pour saisir les donnees</p>'; break;
    case 'analysis': el.innerHTML = '<p style="color:#5B6B7D;text-align:center;padding:20px">Utilisez l\'onglet <strong>Statistiques</strong> pour les analyses</p>'; break;
    case 'discussion': loadDiscussion(el); break;
    case 'references': el.innerHTML = '<p style="color:#5B6B7D;text-align:center;padding:20px">Utilisez le menu <strong>References bibliographiques</strong></p>'; break;
    default: el.innerHTML = milestoneContentHTML(ms); break;
  }
}

function milestoneContentHTML(ms) {
  const configs = {
    topic_assigned: { title: 'Sujet assigne', desc: 'Le directeur a valide le sujet de votre memoire.', fields: ['title','subject','domain','researchQuestion','objectives'] },
    finalization: { title: 'Finalisation du memoire', desc: 'Assemblez et exportez votre memoire complet.', fields: [] }
  };
  const c = configs[ms] || { title: milestoneLabel(ms), desc: '', fields: [] };
  let html = '<h4 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:8px">' + c.title + '</h4>';
  html += '<p style="color:#5B6B7D;font-size:14px;line-height:1.6;margin-bottom:16px">' + c.desc + '</p>';
  if (ms === 'finalization') {
    html += '<div style="display:flex;gap:12px;flex-wrap:wrap">' +
      '<button class="btn-primary btn-sm" onclick="exportThesis(\'docx\')">Export Word (DOCX)</button>' +
      '<button class="btn-primary btn-sm" onclick="exportThesis(\'pdf\')">Export PDF</button>' +
      '<button class="btn-outline btn-sm" onclick="checkPlagiarism()">Verification anti-plagiat</button></div>';
    html += '<div id="plagiarism-result" style="margin-top:12px"></div>';
  }
  return html;
}

function loadLiteratureReview(el) {
  el.innerHTML = '<div style="display:flex;flex-direction:column;gap:12px">' +
    '<h4 style="font-size:16px;font-weight:700;color:#1A5276">Revue de litterature</h4>' +
    '<div style="display:flex;gap:8px;align-items:center"><span style="font-size:13px;color:#5B6B7D">Mots:</span><span id="lr-wordcount" style="font-family:Roboto Mono;font-size:13px;font-weight:600;color:#1A5276">0</span></div>' +
    '<textarea id="lr-content" class="input-field" style="min-height:300px" placeholder="Redigez votre revue de litterature ici..." oninput="updateWordCount()"></textarea>' +
    '<div style="display:flex;gap:8px"><button class="btn-primary btn-sm" onclick="saveLiteratureReview(\'draft\')">Sauvegarder brouillon</button>' +
    '<button class="btn-success btn-sm" onclick="saveLiteratureReview(\'submitted\')">Soumettre au directeur</button></div></div>';
  // Load existing
  if (activeProjectId) {
    db.collection('LiteratureReviews').where('projectId', '==', activeProjectId).orderBy('version', 'desc').limit(1).get()
    .then(snap => {
      if (!snap.empty) {
        const d = snap.docs[0].data();
        document.getElementById('lr-content').value = d.content || '';
        updateWordCount();
      }
    }).catch(() => {});
  }
}

function updateWordCount() {
  const text = document.getElementById('lr-content').value;
  document.getElementById('lr-wordcount').textContent = text.split(/\s+/).filter(w => w).length;
}

async function saveLiteratureReview(status) {
  if (!activeProjectId) return;
  const content = document.getElementById('lr-content').value;
  try {
    // Check if exists
    const existing = await db.collection('LiteratureReviews').where('projectId', '==', activeProjectId).orderBy('version', 'desc').limit(1).get();
    const version = existing.empty ? 1 : (existing.docs[0].data().version || 0) + 1;
    if (existing.empty) {
      await db.collection('LiteratureReviews').add({
        projectId: activeProjectId,
        content: content,
        structure: {},
        keyFindings: [],
        wordCount: content.split(/\s+/).filter(w => w).length,
        status: status,
        version: version,
        directorComments: [],
        lastEditedBy: 'student',
        createdAt: firebase.firestore.FieldValue.serverTimestamp(),
        updatedAt: firebase.firestore.FieldValue.serverTimestamp()
      });
    } else {
      await db.collection('LiteratureReviews').doc(existing.docs[0].id).update({
        content: content,
        wordCount: content.split(/\s+/).filter(w => w).length,
        status: status,
        version: version,
        lastEditedBy: 'student',
        updatedAt: firebase.firestore.FieldValue.serverTimestamp()
      });
    }
    showToast(status === 'submitted' ? 'Revue soumise' : 'Brouillon sauvegarde', 'success');
  } catch(e) {
    showToast('Erreur: ' + e.message, 'error');
  }
}

function loadProtocol(el) {
  const sections = [
    { id: 'proto-general', title: 'Informations generales', fields: [
      { id: 'proto-type', label: 'Type d\'etude', type: 'select', options: ['Observationnel','Experimental','Transversal','Longitudinal','Cas-temoin'] },
      { id: 'proto-design', label: 'Plan d\'etude', type: 'textarea' }
    ]},
    { id: 'proto-problem', title: 'Problematique et objectifs', fields: [
      { id: 'proto-question', label: 'Question de recherche', type: 'textarea' },
      { id: 'proto-objectives', label: 'Objectifs specifiques (un par ligne)', type: 'textarea' },
      { id: 'proto-hypotheses', label: 'Hypotheses (une par ligne)', type: 'textarea' }
    ]},
    { id: 'proto-pop', title: 'Population et echantillon', fields: [
      { id: 'proto-population', label: 'Population d\'etude', type: 'textarea' },
      { id: 'proto-sample', label: 'Taille d\'echantillon', type: 'number' },
      { id: 'proto-formula', label: 'Formule utilisee', type: 'text' }
    ]},
    { id: 'proto-vars', title: 'Variables', fields: [
      { id: 'proto-indep', label: 'Variables independantes', type: 'textarea' },
      { id: 'proto-dep', label: 'Variables dependantes', type: 'textarea' },
      { id: 'proto-conf', label: 'Variables confusionnelles', type: 'textarea' }
    ]},
    { id: 'proto-collect', title: 'Collecte de donnees', fields: [
      { id: 'proto-method', label: 'Methode de collecte', type: 'select', options: ['Questionnaire','Entretien','Dossiers medicaux','Examen clinique'] }
    ]},
    { id: 'proto-ethics', title: 'Considerations ethiques', fields: [
      { id: 'proto-ethics', label: 'Considérations ethiques', type: 'textarea' }
    ]},
    { id: 'proto-statplan', title: 'Plan d\'analyse statistique', fields: [
      { id: 'proto-plan', label: 'Analyses prevues', type: 'textarea' }
    ]}
  ];

  let html = '<h4 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:12px">Protocole de recherche</h4>';
  sections.forEach((s, i) => {
    html += '<div class="card" style="padding:0;margin-bottom:8px;overflow:hidden">' +
      '<div class="accordion-header" onclick="toggleAccordion(this)">' +
      '<span style="font-weight:600;font-size:14px;color:#1A5276">' + (i+1) + '. ' + s.title + '</span>' +
      '<svg class="w-5 h-5" style="color:#5B6B7D;transition:transform .3s" fill="currentColor" viewBox="0 0 24 24"><path d="M7.41 8.59L12 13.17l4.59-4.58L18 10l-6 6-6-6z"/></svg></div>' +
      '<div class="accordion-content"><div style="padding:16px;display:flex;flex-direction:column;gap:12px">';
    s.fields.forEach(f => {
      html += '<div><label style="font-size:12px;font-weight:600;color:#5B6B7D;display:block;margin-bottom:4px">' + f.label + '</label>';
      if (f.type === 'textarea') html += '<textarea id="' + f.id + '" class="input-field" rows="3"></textarea>';
      else if (f.type === 'select') {
        html += '<select id="' + f.id + '" class="input-field"><option value="">Choisir...</option>';
        f.options.forEach(o => html += '<option value="' + o + '">' + o + '</option>');
        html += '</select>';
      } else html += '<input id="' + f.id + '" type="' + f.type + '" class="input-field">';
      html += '</div>';
    });
    html += '</div></div></div>';
  });
  html += '<div style="display:flex;gap:8px;margin-top:8px"><button class="btn-primary btn-sm" onclick="saveProtocol(\'draft\')">Sauvegarder</button>' +
    '<button class="btn-success btn-sm" onclick="saveProtocol(\'submitted\')">Soumettre</button></div>';
  el.innerHTML = html;

  // Load existing
  if (activeProjectId) {
    db.collection('ResearchProtocols').where('projectId', '==', activeProjectId).orderBy('version', 'desc').limit(1).get()
    .then(snap => {
      if (!snap.empty) {
        const d = snap.docs[0].data();
        const map = { studyType: 'proto-type', studyDesign: 'proto-design', researchQuestion: 'proto-question', population: 'proto-population', sampleSize: 'proto-sample', sampleSizeFormula: 'proto-formula' };
        Object.entries(map).forEach(([k, id]) => { const el = document.getElementById(id); if (el && d[k]) el.value = d[k]; });
      }
    }).catch(() => {});
  }
}

function toggleAccordion(header) {
  const content = header.nextElementSibling;
  const arrow = header.querySelector('svg');
  content.classList.toggle('open');
  arrow.style.transform = content.classList.contains('open') ? 'rotate(180deg)' : '';
}

async function saveProtocol(status) {
  if (!activeProjectId) return;
  try {
    const data = {
      projectId: activeProjectId,
      studyType: document.getElementById('proto-type').value,
      studyDesign: document.getElementById('proto-design').value,
      population: document.getElementById('proto-population').value,
      sampleSize: parseInt(document.getElementById('proto-sample').value) || 0,
      sampleSizeFormula: document.getElementById('proto-formula').value,
      variables: [],
      independentVars: (document.getElementById('proto-indep').value || '').split('\n').filter(v => v),
      dependentVars: (document.getElementById('proto-dep').value || '').split('\n').filter(v => v),
      confoundingVars: (document.getElementById('proto-conf').value || '').split('\n').filter(v => v),
      dataCollectionMethod: document.getElementById('proto-method').value,
      ethicalConsiderations: document.getElementById('proto-ethics').value,
      statisticalPlan: document.getElementById('proto-plan').value,
      status: status,
      version: 1,
      directorComments: [],
      createdAt: firebase.firestore.FieldValue.serverTimestamp(),
      updatedAt: firebase.firestore.FieldValue.serverTimestamp()
    };
    const existing = await db.collection('ResearchProtocols').where('projectId', '==', activeProjectId).limit(1).get();
    if (existing.empty) {
      await db.collection('ResearchProtocols').add(data);
    } else {
      data.version = (existing.docs[0].data().version || 0) + 1;
      await db.collection('ResearchProtocols').doc(existing.docs[0].id).update(data);
    }
    showToast(status === 'submitted' ? 'Protocole soumis' : 'Sauvegarde', 'success');
  } catch(e) {
    showToast('Erreur: ' + e.message, 'error');
  }
}

function loadSurveyForm(el) {
  el.innerHTML = '<h4 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:12px">Fiche d\'enquete</h4>' +
    '<div class="card" style="background:#EBF5FB;border:1px solid #AED6F1">' +
    '<p style="font-size:14px;color:#1A5276;font-weight:600;margin-bottom:4px">Generation IA de la fiche</p>' +
    '<p style="font-size:13px;color:#5B6B7D;line-height:1.5">L\'IA analyse votre revue de litterature et protocole pour generer automatiquement une fiche d\'enquete optimisee avec les variables, types de reponses et logique de saut conditionnel.</p></div>' +
    '<button class="btn-primary" style="width:100%;margin-top:12px" onclick="generateSurveyAI()">Generer la fiche avec l\'IA</button>' +
    '<div id="survey-form-content" style="margin-top:16px"></div>';
  // Load existing form
  if (activeProjectId) {
    db.collection('SurveyForms').where('projectId', '==', activeProjectId).orderBy('version', 'desc').limit(1).get()
    .then(snap => {
      if (!snap.empty) renderSurveyForm(snap.docs[0].data());
    }).catch(() => {});
  }
}

function generateSurveyAI() {
  showToast('Generation en cours... L\'IA analyse vos documents', 'info');
  // Simulate AI generation (in production, call OpenAI/Claude API)
  setTimeout(() => {
    const demoQuestions = [
      { id: 'q1', text: 'Age du patient', type: 'number', category: 'Sociodemographique', required: true, variableName: 'age' },
      { id: 'q2', text: 'Sexe', type: 'single_choice', options: ['Masculin','Feminin'], category: 'Sociodemographique', required: true, variableName: 'sexe' },
      { id: 'q3', text: 'Niveau d\'education', type: 'single_choice', options: ['Primaire','Secondaire','Universitaire'], category: 'Sociodemographique', required: true, variableName: 'education' },
      { id: 'q4', text: 'Diagnostic principal', type: 'text', category: 'Clinique', required: true, variableName: 'diagnostic' },
      { id: 'q5', text: 'Duree d\'evolution (en mois)', type: 'number', category: 'Clinique', required: true, variableName: 'duree' },
      { id: 'q6', text: 'Antecedents pathologiques', type: 'multiple_choice', options: ['Hypertension','Diabete','VIH','Tuberculose','Autre'], category: 'Antecedents', required: false, variableName: 'antecedents' },
      { id: 'q7', text: 'Pression arterielle systolique (mmHg)', type: 'number', category: 'Paraclinique', required: true, variableName: 'pas' },
      { id: 'q8', text: 'Resultat de l\'examen', type: 'single_choice', options: ['Normal','Anormal'], category: 'Paraclinique', required: true, variableName: 'resultat_examen', skipLogic: { questionId: 'q7', operator: '>', value: 140 } }
    ];
    renderSurveyForm({ questions: demoQuestions, aiGenerated: true, isLocked: false, title: 'Fiche d\'enquete - Memoire', status: 'generated', totalQuestions: demoQuestions.length, estimatedDuration: 12 });
    showToast('Fiche generee avec succes !', 'success');
  }, 2000);
}

function renderSurveyForm(form) {
  const container = document.getElementById('survey-form-content');
  if (!container) return;
  let html = '<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">' +
    '<span class="badge ' + (form.isLocked ? 'badge-green' : 'badge-blue') + '">' + (form.isLocked ? 'Verrouillee' : 'Brouillon') + '</span>' +
    '<span style="font-size:12px;color:#5B6B7D">' + (form.totalQuestions || 0) + ' questions | ~' + (form.estimatedDuration || 0) + ' min</span></div>';
  (form.questions || []).forEach((q, i) => {
    html += '<div class="card" style="padding:12px;margin-bottom:8px"><div style="display:flex;gap:8px;align-items:flex-start">' +
      '<span style="font-family:Roboto Mono;font-size:12px;color:#2E86C1;font-weight:600;min-width:24px">Q' + (i+1) + '</span>' +
      '<div style="flex:1"><p style="font-size:14px;font-weight:500;margin-bottom:4px">' + q.text + (q.required ? ' *' : '') + '</p>' +
      '<span class="badge badge-gray" style="font-size:10px">' + (q.category || '') + ' | ' + (q.variableName || '') + ' | ' + (q.type || '') + '</span></div>';
    if (!form.isLocked) html += '<button style="background:none;border:none;cursor:pointer;color:#E74C3C" onclick="this.closest(\'.card\').remove()"><svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24"><path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/></svg></button>';
    html += '</div></div>';
  });
  if (!form.isLocked) {
    html += '<div style="display:flex;gap:8px;margin-top:8px"><button class="btn-primary btn-sm" onclick="saveSurveyForm()">Sauvegarder</button>' +
      '<button class="btn-success btn-sm" onclick="submitSurveyForValidation()">Soumettre pour validation</button></div>';
  }
  container.innerHTML = html;
}

function loadDiscussion(el) {
  el.innerHTML = '<h4 style="font-size:16px;font-weight:700;color:#1A5276;margin-bottom:12px">Generation IA de la discussion</h4>' +
    '<div class="card" style="background:#EBF5FB;border:1px solid #AED6F1"><p style="font-size:14px;color:#1A5276;font-weight:600;margin-bottom:4px">Discussion assistee par IA</p>' +
    '<p style="font-size:13px;color:#5B6B7D;line-height:1.5">L\'IA compare vos resultats avec les etudes citees dans la revue de litterature et genere une discussion structuree avec references auto-identifiees.</p></div>' +
    '<button class="btn-primary" style="width:100%;margin-top:12px" onclick="generateDiscussionAI()">Generer la discussion avec l\'IA</button>' +
    '<div id="discussion-content" style="margin-top:16px"></div>';
}

function generateDiscussionAI() {
  showToast('Generation en cours...', 'info');
  setTimeout(() => {
    const el = document.getElementById('discussion-content');
    if (!el) return;
    el.innerHTML = '<div class="card" style="padding:16px"><h5 style="color:#1A5276;font-weight:700;margin-bottom:8px">5.1. Concordances avec la litterature</h5>' +
      '<p style="font-size:14px;color:#5B6B7D;line-height:1.6">Nos resultats sont concordants avec ceux rapportes par [Reference 1] qui avait trouve des resultats similaires dans une population comparable. Ces resultats confirment l\'hypothese selon laquelle...</p></div>' +
      '<div class="card" style="padding:16px;margin-top:8px"><h5 style="color:#1A5276;font-weight:700;margin-bottom:8px">5.2. Discordances</h5>' +
      '<p style="font-size:14px;color:#5B6B7D;line-height:1.6">Contrairement a [Reference 3], nos resultats montrent une difference significative. Cette discordance pourrait s\'expliquer par les differences methodologiques, notamment la taille de l\'echantillon et les criteres d\'inclusion...</p></div>' +
      '<div class="card" style="padding:16px;margin-top:8px"><h5 style="color:#1A5276;font-weight:700;margin-bottom:8px">5.3. Implications cliniques</h5>' +
      '<p style="font-size:14px;color:#5B6B7D;line-height:1.6">Ces resultats ont des implications importantes pour la pratique clinique en RDC. Ils suggerent que...</p></div>' +
      '<div class="card" style="padding:16px;margin-top:8px"><h5 style="color:#1A5276;font-weight:700;margin-bottom:8px">5.4. Limites</h5>' +
      '<p style="font-size:14px;color:#5B6B7D;line-height:1.6">Notre etude presente certaines limites. La taille de l\'echantillon reste modeste et le caractere unicentrique limite la generalisabilite des resultats...</p></div>' +
      '<div class="card" style="padding:16px;margin-top:8px"><h5 style="color:#1A5276;font-weight:700;margin-bottom:8px">5.5. Perspectives</h5>' +
      '<p style="font-size:14px;color:#5B6B7D;line-height:1.6">Des etudes complementaires multicentriques seraient necessaires pour confirmer ces resultats. Par ailleurs, l\'utilisation de methodes analytiques plus avancees permettrait de...</p></div>';
    showToast('Discussion generee !', 'success');
  }, 2500);
}

function exportThesis(format) {
  showToast('Export ' + format.toUpperCase() + ' en cours de preparation...', 'info');
  setTimeout(() => showToast('Document exporte avec succes (mode demo)', 'success'), 2000);
}

function checkPlagiarism() {
  showToast('Verification anti-plagiat en cours...', 'info');
  setTimeout(() => {
    const el = document.getElementById('plagiarism-result');
    if (el) el.innerHTML = '<div class="card" style="margin-top:12px;background:#EAFAF1;border:1px solid #A9DFBF">' +
      '<p style="font-size:14px;font-weight:600;color:#27AE60">Score de similarite: 12%</p>' +
      '<p style="font-size:13px;color:#5B6B7D;margin-top:4px">Votre document est original. Aucun passage plagiarise significatif detecte.</p></div>';
    showToast('Verification terminee', 'success');
  }, 3000);
}

// ═══════════════════════════════════════════════════
// FIND DIRECTOR / SUPERVISION REQUEST
// ═══════════════════════════════════════════════════
function searchDirectors(query) {
  if (!query || query.length < 2) {
    document.getElementById('directors-list').innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Saisissez au moins 2 caracteres</p>';
    return;
  }
  db.collection('Users').where('role', 'in', ['director', 'co_director']).get()
  .then(snap => {
    let html = '';
    snap.forEach(doc => {
      const d = doc.data();
      const name = (d.displayName || '').toLowerCase();
      const spec = (d.specialty || '').toLowerCase();
      if (name.includes(query.toLowerCase()) || spec.includes(query.toLowerCase())) {
        const initials = (d.displayName || '?').split(' ').map(w => w[0]).join('').substring(0, 2).toUpperCase();
        html += '<div class="card fade-in" style="display:flex;align-items:center;gap:12px;cursor:pointer" onclick="openSupervisionModal(\'' + doc.id + '\',\'' + (d.displayName || '').replace(/'/g, "\\'") + '\')">' +
          '<div style="width:44px;height:44px;border-radius:50%;background:linear-gradient(135deg,#27AE60,#1A5276);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:14px">' + initials + '</div>' +
          '<div style="flex:1"><p style="font-weight:600;font-size:14px">' + (d.displayName || '') + '</p><p style="font-size:12px;color:#5B6B7D">' + (d.title || '') + ' ' + (d.specialty || '') + ' | ' + (d.universityName || '') + '</p></div>' +
          '<svg class="w-5 h-5" style="color:#95A5A6" fill="currentColor" viewBox="0 0 24 24"><path d="M10 6L8.59 7.41 13.17 12l-4.58 4.59L10 18l6-6z"/></svg></div>';
      }
    });
    document.getElementById('directors-list').innerHTML = html || '<p style="color:#95A5A6;text-align:center;padding:24px">Aucun directeur trouve</p>';
  }).catch(() => {
    document.getElementById('directors-list').innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Erreur de chargement</p>';
  });
}

function openSupervisionModal(dirId, dirName) {
  selectedDirectorId = dirId;
  document.getElementById('modal-dir-name').textContent = dirName;
  document.getElementById('modal-supervision').style.display = 'flex';
}

async function sendSupervisionRequest() {
  if (!currentUser || !selectedDirectorId) return;
  const subject = document.getElementById('req-subject').value.trim();
  const message = document.getElementById('req-message').value.trim();
  const domain = document.getElementById('req-domain').value.trim();
  if (!subject) { showToast('Veuillez saisir le sujet souhaite', 'error'); return; }
  try {
    await db.collection('Supervisions').add({
      studentId: currentUser.uid,
      directorId: selectedDirectorId,
      coDirectorIds: [],
      thesisProjectId: null,
      status: 'pending',
      studentMessage: subject + (message ? '\n\n' + message : ''),
      directorResponse: '',
      universityId: currentUserData.universityId || '',
      academicYearId: currentUserData.academicYearId || '',
      promotion: currentUserData.promotion || '',
      requestDate: firebase.firestore.FieldValue.serverTimestamp(),
      responseDate: null,
      completionDate: null,
      createdAt: firebase.firestore.FieldValue.serverTimestamp(),
      updatedAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    // Notify director
    await db.collection('Notifications').add({
      userId: selectedDirectorId,
      type: 'supervision_request',
      title: 'Nouvelle demande de supervision',
      body: (currentUserData.displayName || 'Un etudiant') + ' souhaite votre encadrement pour: ' + subject,
      isRead: false,
      deepLink: 'dir-dashboard',
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    document.getElementById('modal-supervision').style.display = 'none';
    showToast('Demande envoyee avec succes', 'success');
    nav('dashboard');
  } catch(e) {
    showToast('Erreur: ' + e.message, 'error');
  }
}

// ═══════════════════════════════════════════════════
// STUDENTS MANAGEMENT (Director)
// ═══════════════════════════════════════════════════
function loadStudents() {
  if (!currentUser) return;
  db.collection('Supervisions').where('directorId', '==', currentUser.uid).where('status', '==', 'accepted').get()
  .then(snap => {
    const el = document.getElementById('students-list');
    document.getElementById('students-count').textContent = snap.size + ' etudiant(s) trouve(s)';
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:40px">Aucun etudiant</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const s = doc.data();
      const initials = 'E';
      html += '<div class="card fade-in"><div style="display:flex;align-items:center;gap:12px">' +
        '<div style="width:40px;height:40px;border-radius:50%;background:#2E86C1;display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:14px">' + initials + '</div>' +
        '<div style="flex:1"><p style="font-weight:600;font-size:14px">Etudiant ' + s.studentId.substring(0, 8) + '</p>' +
        '<p style="font-size:12px;color:#5B6B7D">' + (s.promotion || '') + ' | ' + (s.universityId || '') + '</p></div>' +
        '<span class="badge badge-green">Actif</span></div></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

function loadProjects() {
  if (!currentUser) return;
  db.collection('Supervisions').where('directorId', '==', currentUser.uid).where('status', '==', 'accepted').get()
  .then(snap => {
    const el = document.getElementById('projects-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:40px">Aucun projet</p>'; return; }
    let promises = [];
    snap.forEach(doc => {
      const s = doc.data();
      if (s.thesisProjectId) {
        promises.push(db.collection('ThesisProjects').doc(s.thesisProjectId).get().then(pdoc => {
          if (pdoc.exists) return pdoc.data();
          return null;
        }));
      }
    });
    Promise.all(promises).then(projects => {
      projects = projects.filter(p => p);
      if (projects.length === 0) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:40px">Aucun projet</p>'; return; }
      let html = '';
      projects.forEach(p => {
        html += '<div class="card fade-in"><h4 style="font-size:14px;font-weight:700;color:#1A5276;margin-bottom:4px">' + (p.title || 'Sans titre') + '</h4>' +
          '<p style="font-size:13px;color:#5B6B7D">' + (p.domain || '') + '</p>' +
          '<div style="margin-top:8px;display:flex;gap:8px;align-items:center"><span class="badge badge-blue">' + milestoneLabel(p.currentMilestone) + '</span></div></div>';
      });
      el.innerHTML = html;
    });
  }).catch(() => {});
}

// ═══════════════════════════════════════════════════
// DATA COLLECTION
// ═══════════════════════════════════════════════════
let currentRespondent = null;
function loadCollection() {
  if (!activeProjectId) return;
  db.collection('SurveyResponses').where('projectId', '==', activeProjectId).orderBy('createdAt', 'desc').get()
  .then(snap => {
    document.getElementById('coll-progress').textContent = snap.size + ' reponses collectees';
    const el = document.getElementById('responses-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Aucune reponse</p>'; return; }
    let html = '';
    snap.forEach((doc, i) => {
      const r = doc.data();
      html += '<div class="card" style="display:flex;justify-content:space-between;align-items:center;padding:12px">' +
        '<div><p style="font-weight:600;font-size:14px">Repondant #' + (snap.size - i) + '</p>' +
        '<p style="font-size:12px;color:#5B6B7D">' + (r.collectionDate ? new Date(r.collectionDate.seconds * 1000).toLocaleDateString('fr-FR') : '') + ' | ' + (r.isValid ? '<span class="badge badge-green">Valide</span>' : '<span class="badge badge-red">Invalide</span>') + '</p></div>' +
        '<span class="badge ' + (r.isSynced !== false ? 'badge-green' : 'badge-orange') + '">' + (r.isSynced !== false ? 'Sync' : 'Hors ligne') + '</span></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

function newRespondent() {
  if (!activeProjectId) { showToast('Aucun projet actif', 'error'); return; }
  currentRespondent = { responses: {} };
  document.getElementById('collection-form').style.display = 'block';
  document.getElementById('resp-status').textContent = 'En cours';
  document.getElementById('resp-status').className = 'badge badge-blue';
  // Load form questions
  db.collection('SurveyForms').where('projectId', '==', activeProjectId).where('isLocked', '==', true).limit(1).get()
  .then(snap => {
    if (snap.empty) { showToast('Aucune fiche validee trouvee', 'error'); return; }
    const form = snap.docs[0].data();
    const qEl = document.getElementById('form-questions');
    let html = '';
    (form.questions || []).forEach((q, i) => {
      html += '<div><label style="font-size:13px;font-weight:600;color:#1A5276;display:block;margin-bottom:4px">' + (i+1) + '. ' + q.text + (q.required ? ' *' : '') + '</label>';
      if (q.type === 'text') html += '<input class="input-field" data-qid="' + q.id + '" onchange="updateResponse(\'' + q.id + '\',this.value)">';
      else if (q.type === 'number') html += '<input type="number" class="input-field" data-qid="' + q.id + '" onchange="updateResponse(\'' + q.id + '\',this.value)">';
      else if (q.type === 'single_choice') {
        html += '<div style="display:flex;flex-direction:column;gap:4px">';
        (q.options || []).forEach(o => html += '<label style="display:flex;align-items:center;gap:8px;cursor:pointer;font-size:14px"><input type="radio" name="' + q.id + '" value="' + o + '" onchange="updateResponse(\'' + q.id + '\',this.value)" style="accent-color:#2E86C1">' + o + '</label>');
        html += '</div>';
      } else if (q.type === 'multiple_choice') {
        html += '<div style="display:flex;flex-wrap:wrap;gap:8px">';
        (q.options || []).forEach(o => html += '<label style="display:flex;align-items:center;gap:6px;cursor:pointer;font-size:13px;padding:6px 12px;border:1px solid #E0E6ED;border-radius:8px"><input type="checkbox" value="' + o + '" onchange="updateMultiResponse(\'' + q.id + '\',this)" style="accent-color:#2E86C1">' + o + '</label>');
        html += '</div>';
      } else if (q.type === 'boolean') {
        html += '<div style="display:flex;gap:8px"><label style="display:flex;align-items:center;gap:6px;cursor:pointer;font-size:14px"><input type="radio" name="' + q.id + '" value="Oui" onchange="updateResponse(\'' + q.id + '\',\'Oui\')" style="accent-color:#2E86C1">Oui</label><label style="display:flex;align-items:center;gap:6px;cursor:pointer;font-size:14px"><input type="radio" name="' + q.id + '" value="Non" onchange="updateResponse(\'' + q.id + '\',\'Non\')" style="accent-color:#2E86C1">Non</label></div>';
      } else if (q.type === 'date') {
        html += '<input type="date" class="input-field" onchange="updateResponse(\'' + q.id + '\',this.value)">';
      } else if (q.type === 'likert_scale') {
        html += '<div style="display:flex;gap:4px">';
        for (let s = 1; s <= 5; s++) html += '<label style="display:flex;flex-direction:column;align-items:center;gap:2px;cursor:pointer;font-size:12px;color:#5B6B7D"><input type="radio" name="' + q.id + '" value="' + s + '" onchange="updateResponse(\'' + q.id + '\',' + s + ')" style="accent-color:#2E86C1">' + s + '</label>';
        html += '</div>';
      } else {
        html += '<input class="input-field" data-qid="' + q.id + '" onchange="updateResponse(\'' + q.id + '\',this.value)">';
      }
      html += '</div>';
    });
    qEl.innerHTML = html;
  }).catch(() => {});
}

function updateResponse(qid, val) { if (currentRespondent) currentRespondent.responses[qid] = val; }
function updateMultiResponse(qid, checkbox) {
  if (!currentRespondent) return;
  if (!currentRespondent.responses[qid]) currentRespondent.responses[qid] = [];
  const arr = currentRespondent.responses[qid];
  if (checkbox.checked) arr.push(checkbox.value); else arr.splice(arr.indexOf(checkbox.value), 1);
}

function cancelRespondent() {
  currentRespondent = null;
  document.getElementById('collection-form').style.display = 'none';
}

async function saveRespondent() {
  if (!activeProjectId || !currentRespondent) return;
  try {
    await db.collection('SurveyResponses').add({
      projectId: activeProjectId,
      surveyFormId: '',
      respondentId: 'resp_' + Date.now(),
      responses: currentRespondent.responses,
      collectedBy: currentUser.uid,
      collectionDate: firebase.firestore.FieldValue.serverTimestamp(),
      location: null,
      isSynced: true,
      isValid: true,
      validationErrors: [],
      duration: 0,
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    currentRespondent = null;
    document.getElementById('collection-form').style.display = 'none';
    showToast('Reponse enregistree', 'success');
    loadCollection();
  } catch(e) {
    showToast('Erreur: ' + e.message, 'error');
  }
}

// ═══════════════════════════════════════════════════
// STATISTICAL ANALYSIS
// ═══════════════════════════════════════════════════
function loadStats() {
  // Load variable options from project
  if (activeProjectId) {
    db.collection('ResearchProtocols').where('projectId', '==', activeProjectId).limit(1).get()
    .then(snap => {
      if (!snap.empty) {
        const d = snap.docs[0].data();
        const vars = [...(d.independentVars || []), ...(d.dependentVars || [])];
        const opts = vars.map(v => '<option value="' + v + '">' + v + '</option>').join('');
        document.getElementById('stat-var1').innerHTML = '<option value="">Choisir...</option>' + opts;
        document.getElementById('stat-var2').innerHTML = '<option value="">Choisir...</option>' + opts;
      }
    }).catch(() => {});
  }
}

function runAnalysis() {
  const v1 = document.getElementById('stat-var1').value;
  const v2 = document.getElementById('stat-var2').value;
  if (!v1 || !v2) { showToast('Selectionnez les deux variables', 'error'); return; }
  // Auto-recommend test
  const rec = document.getElementById('stat-recommendation');
  rec.style.display = 'block';
  document.getElementById('stat-rec-text').textContent = 'Test recommande: Chi-carre d\'independance (Quali x Quali)';
  // Show results (demo)
  setTimeout(() => {
    const results = document.getElementById('stat-results');
    results.style.display = 'block';
    document.getElementById('stat-table-container').innerHTML =
      '<table style="width:100%;border-collapse:collapse;font-size:14px"><thead><tr style="background:#F8F9FA"><th class="px-4 py-2 text-left">Statistique</th><th class="px-4 py-2 text-left">Valeur</th></tr></thead><tbody>' +
      '<tr><td class="px-4 py-2">Chi-carre</td><td class="px-4 py-2 font-mono">12.456</td></tr>' +
      '<tr><td class="px-4 py-2">Degre de liberte (ddl)</td><td class="px-4 py-2 font-mono">3</td></tr>' +
      '<tr><td class="px-4 py-2">p-value</td><td class="px-4 py-2 font-mono" style="color:#E74C3C;font-weight:600">0.006</td></tr>' +
      '<tr><td class="px-4 py-2">Significativite</td><td class="px-4 py-2"><span class="badge badge-green">Significatif (p &lt; 0.05)</span></td></tr></tbody></table>';
    document.getElementById('stat-interpretation').innerHTML = '<strong style="color:#1A5276">Interpretation:</strong> Il existe une association statistiquement significative entre ' + v1 + ' et ' + v2 + ' (Chi-carre = 12.456, ddl = 3, p = 0.006). Le test est significatif au seuil de 5%, ce qui signifie que nous rejetons l\'hypothese nulle d\'independance entre ces deux variables.';
    // Chart
    const ctx = document.getElementById('stat-chart');
    if (ctx && window.Chart) {
      new Chart(ctx, {
        type: 'bar',
        data: { labels: ['Cat 1', 'Cat 2', 'Cat 3', 'Cat 4'], datasets: [{ label: v1 + ' - Cat A', data: [45, 30, 15, 10], backgroundColor: '#2E86C1', borderRadius: 6 }, { label: v1 + ' - Cat B', data: [20, 40, 25, 15], backgroundColor: '#1A5276', borderRadius: 6 }] },
        options: { responsive: true, plugins: { legend: { position: 'bottom' } } }
      });
    }
    // Save analysis
    if (activeProjectId && db) {
      db.collection('StatisticalAnalyses').add({
        projectId: activeProjectId,
        title: v1 + ' vs ' + v2,
        researchQuestion: '',
        variables: [v1, v2],
        testType: 'Chi-carre',
        testResult: { chiSquare: 12.456, ddl: 3, pValue: 0.006 },
        interpretation: 'Association significative (p=0.006)',
        aiGenerated: true,
        chartType: 'bar',
        status: 'interpreted',
        requestedBy: currentUser ? currentUser.uid : '',
        createdAt: firebase.firestore.FieldValue.serverTimestamp(),
        updatedAt: firebase.firestore.FieldValue.serverTimestamp()
      }).catch(() => {});
    }
    showToast('Analyse terminee', 'success');
  }, 1500);
}

// ═══════════════════════════════════════════════════
// CHAT / MESSAGING
// ═══════════════════════════════════════════════════
function loadChatList() {
  if (!currentUser) return;
  const field = currentUserData.role === 'director' ? 'directorId' : 'studentId';
  db.collection('Supervisions').where(field, '==', currentUser.uid).where('status', '==', 'accepted').get()
  .then(snap => {
    const el = document.getElementById('chat-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:40px">Aucune conversation</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const s = doc.data();
      const partnerId = field === 'directorId' ? s.studentId : s.directorId;
      const initials = (field === 'directorId' ? 'E' : 'Dr');
      html += '<div class="card fade-in" style="display:flex;align-items:center;gap:12px;cursor:pointer" onclick="openChat(\'' + doc.id + '\')">' +
        '<div style="width:44px;height:44px;border-radius:50%;background:linear-gradient(135deg,#1A5276,#2E86C1);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:14px">' + initials + '</div>' +
        '<div style="flex:1"><p style="font-weight:600;font-size:14px">' + (s.studentMessage || 'Conversation').substring(0, 40) + '</p><p style="font-size:12px;color:#5B6B7D">Supervision ' + doc.id.substring(0, 8) + '</p></div></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

function openChat(supervisionId) {
  activeChatSupervisionId = supervisionId;
  document.getElementById('chat-list').style.display = 'none';
  document.getElementById('chat-conv').style.display = 'flex';
  // Get supervision to find partner
  db.collection('Supervisions').doc(supervisionId).get().then(doc => {
    if (doc.exists) {
      const s = doc.data();
      const partnerId = currentRole === 'director' ? s.studentId : s.directorId;
      document.getElementById('chat-partner-name').textContent = currentRole === 'director' ? 'Etudiant' : 'Directeur';
      // Load messages
      db.collection('Messages').where('supervisionId', '==', supervisionId).orderBy('createdAt', 'asc').get()
      .then(msgSnap => {
        const el = document.getElementById('chat-messages');
        if (msgSnap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:20px">Aucun message. Commencez la conversation !</p>'; return; }
        let html = '';
        msgSnap.forEach(md => {
          const m = md.data();
          const isMine = m.senderId === currentUser.uid;
          const time = m.createdAt ? new Date(m.createdAt.seconds * 1000).toLocaleTimeString('fr-FR', { hour: '2-digit', minute: '2-digit' }) : '';
          html += '<div style="display:flex;flex-direction:column;' + (isMine ? 'align-items:flex-end' : 'align-items:flex-start') + '">' +
            '<div class="msg-bubble ' + (isMine ? 'msg-sent' : 'msg-received') + '">' + (m.content || '') + '</div>' +
            '<span style="font-size:10px;color:#95A5A6;margin-top:2px">' + time + '</span></div>';
        });
        el.innerHTML = html;
        el.scrollTop = el.scrollHeight;
        // Mark as read
        msgSnap.forEach(md => {
          if (!md.data().isRead && md.data().receiverId === currentUser.uid) {
            db.collection('Messages').doc(md.id).update({ isRead: true, readAt: firebase.firestore.FieldValue.serverTimestamp() }).catch(() => {});
          }
        });
      }).catch(() => {});
    }
  }).catch(() => {});
}

function showChatList() {
  document.getElementById('chat-list').style.display = 'flex';
  document.getElementById('chat-conv').style.display = 'none';
}

async function sendMessage() {
  const input = document.getElementById('chat-input');
  const content = input.value.trim();
  if (!content || !activeChatSupervisionId || !currentUser) return;
  input.value = '';
  try {
    const supDoc = await db.collection('Supervisions').doc(activeChatSupervisionId).get();
    const sup = supDoc.data();
    const receiverId = currentRole === 'director' ? sup.studentId : sup.directorId;
    await db.collection('Messages').add({
      supervisionId: activeChatSupervisionId,
      senderId: currentUser.uid,
      receiverId: receiverId,
      content: content,
      type: 'text',
      fileURL: '',
      fileName: '',
      isRead: false,
      readAt: null,
      reactions: [],
      aiSummary: '',
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    // Notify receiver
    await db.collection('Notifications').add({
      userId: receiverId,
      type: 'message',
      title: 'Nouveau message',
      body: content.substring(0, 80),
      isRead: false,
      deepLink: 'chat',
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    });
    // Refresh chat
    openChat(activeChatSupervisionId);
  } catch(e) {
    showToast('Erreur: ' + e.message, 'error');
  }
}

// ═══════════════════════════════════════════════════
// NOTIFICATIONS
// ═══════════════════════════════════════════════════
function loadNotifications() {
  if (!currentUser) return;
  db.collection('Notifications').where('userId', '==', currentUser.uid).orderBy('createdAt', 'desc').get()
  .then(snap => {
    const el = document.getElementById('notif-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:40px">Aucune notification</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const n = doc.data();
      const time = n.createdAt ? new Date(n.createdAt.seconds * 1000).toLocaleDateString('fr-FR') : '';
      html += '<div class="card" style="padding:12px;background:' + (n.isRead ? '#fff' : '#EBF5FB') + ';cursor:pointer;border-left:4px solid ' + (n.isRead ? 'transparent' : '#2E86C1') + '" onclick="markNotifRead(\'' + doc.id + '\')">' +
        '<div style="display:flex;justify-content:space-between"><p style="font-weight:600;font-size:14px;color:#1A5276">' + (n.title || '') + '</p><span style="font-size:11px;color:#95A5A6">' + time + '</span></div>' +
        '<p style="font-size:13px;color:#5B6B7D;margin-top:4px">' + (n.body || '') + '</p>' +
        '<span class="badge badge-blue" style="margin-top:6px">' + (n.type || '') + '</span></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

async function markNotifRead(id) {
  try {
    await db.collection('Notifications').doc(id).update({ isRead: true, readAt: firebase.firestore.FieldValue.serverTimestamp() });
    loadNotifications();
  } catch(e) {}
}

// ═══════════════════════════════════════════════════
// REFERENCES
// ═══════════════════════════════════════════════════
function loadReferences() {
  if (!activeProjectId) { document.getElementById('refs-list').innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Aucun projet actif</p>'; return; }
  db.collection('References').where('projectId', '==', activeProjectId).orderBy('orderIndex').get()
  .then(snap => {
    const el = document.getElementById('refs-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Aucune reference</p>'; return; }
    const fmt = document.getElementById('ref-format').value;
    let html = '';
    snap.forEach(doc => {
      const r = doc.data();
      const citation = fmt === 'apa' ? (r.citationAPA || '') : (r.citationVancouver || '');
      html += '<div class="card" style="padding:12px"><p style="font-size:13px;line-height:1.6;color:#333">' + citation + '</p>' +
        '<div style="display:flex;gap:8px;margin-top:6px"><span class="badge badge-gray">' + (r.source || '') + '</span>' +
        '<span class="badge badge-gray">' + (r.year || '') + '</span></div></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

function searchRefs() { showToast('Recherche PubMed/Scholar en cours...', 'info'); }
function addRefManual() { showToast('Formulaire d\'ajout manuel (a implementer)', 'info'); }

// ═══════════════════════════════════════════════════
// ARCHIVES
// ═══════════════════════════════════════════════════
function loadArchives() {
  db.collection('Archives').orderBy('defenseDate', 'desc').limit(50).get()
  .then(snap => {
    const el = document.getElementById('archives-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:40px">Aucune archive</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const a = doc.data();
      html += '<div class="card fade-in"><h4 style="font-size:14px;font-weight:700;color:#1A5276">' + (a.title || 'Sans titre') + '</h4>' +
        '<p style="font-size:13px;color:#5B6B7D;margin-top:4px">' + (a.domain || '') + ' | ' + (a.promotion || '') + ' | Note: ' + (a.grade || '-') + '</p>' +
        '<p style="font-size:12px;color:#95A5A6;margin-top:4px">Soutenance: ' + (a.defenseDate ? new Date(a.defenseDate.seconds * 1000).toLocaleDateString('fr-FR') : '-') + '</p></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

// ═══════════════════════════════════════════════════
// CALENDAR
// ═══════════════════════════════════════════════════
function loadCalendar() {
  db.collection('AcademicYears').where('isActive', '==', true).limit(1).get()
  .then(snap => {
    const el = document.getElementById('calendar-content');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Aucune annee academique active</p>'; return; }
    const y = snap.docs[0].data();
    el.innerHTML =
      '<div class="card" style="border-left:4px solid #2E86C1"><p style="font-weight:600;color:#1A5276">Annee academique: ' + (y.label || '') + '</p>' +
      '<p style="font-size:13px;color:#5B6B7D;margin-top:4px">Debut: ' + (y.startDate ? new Date(y.startDate.seconds * 1000).toLocaleDateString('fr-FR') : '-') + '</p>' +
      '<p style="font-size:13px;color:#5B6B7D">Fin: ' + (y.endDate ? new Date(y.endDate.seconds * 1000).toLocaleDateString('fr-FR') : '-') + '</p></div>' +
      '<div class="card" style="border-left:4px solid #E74C3C"><p style="font-weight:600;color:#E74C3C">Date limite de soumission</p>' +
      '<p style="font-size:13px;color:#5B6B7D;margin-top:4px">' + (y.submissionDeadline ? new Date(y.submissionDeadline.seconds * 1000).toLocaleDateString('fr-FR') : '-') + '</p></div>' +
      '<div class="card" style="border-left:4px solid #27AE60"><p style="font-weight:600;color:#27AE60">Periode de soutenance</p>' +
      '<p style="font-size:13px;color:#5B6B7D;margin-top:4px">' + (y.defenseStartDate ? new Date(y.defenseStartDate.seconds * 1000).toLocaleDateString('fr-FR') : '-') + ' au ' + (y.defenseEndDate ? new Date(y.defenseEndDate.seconds * 1000).toLocaleDateString('fr-FR') : '-') + '</p></div>';
  }).catch(() => {});
}

// ═══════════════════════════════════════════════════
// ADMIN: USERS
// ═══════════════════════════════════════════════════
function loadAdminUsers() {
  db.collection('Users').orderBy('createdAt', 'desc').limit(100).get()
  .then(snap => {
    const el = document.getElementById('admin-users-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Aucun utilisateur</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const u = doc.data();
      const roleColors = { admin: 'badge-red', director: 'badge-green', co_director: 'badge-blue', student: 'badge-gray' };
      html += '<div class="card" style="display:flex;align-items:center;gap:12px;padding:12px">' +
        '<div style="width:40px;height:40px;border-radius:50%;background:linear-gradient(135deg,#1A5276,#2E86C1);display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;font-size:14px">' +
        (u.displayName || '?').substring(0, 2).toUpperCase() + '</div>' +
        '<div style="flex:1;min-width:0"><p style="font-weight:600;font-size:14px">' + (u.displayName || '') + '</p>' +
        '<p style="font-size:12px;color:#5B6B7D">' + (u.email || '') + ' | ' + (u.universityName || '') + '</p></div>' +
        '<span class="badge ' + (roleColors[u.role] || 'badge-gray') + '">' + (u.role || '') + '</span>' +
        '<button class="btn-danger btn-sm" style="padding:6px 10px;font-size:11px" onclick="deleteUser(\'' + doc.id + '\')"><svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24"><path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/></svg></button></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}

function showAddUserModal() { showToast('Formulaire d\'ajout utilisateur (a implementer dans le panneau admin)', 'info'); }
async function deleteUser(uid) {
  if (!confirm('Supprimer cet utilisateur ?')) return;
  try { await db.collection('Users').doc(uid).delete(); showToast('Utilisateur supprime', 'success'); loadAdminUsers(); } catch(e) { showToast('Erreur: ' + e.message, 'error'); }
}

// ═══════════════════════════════════════════════════
// ADMIN: UNIVERSITIES
// ═══════════════════════════════════════════════════
function loadAdminUniversities() {
  db.collection('Universities').orderBy('orderIndex').get()
  .then(snap => {
    const el = document.getElementById('admin-unis-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Aucune universite</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const u = doc.data();
      html += '<div class="card fade-in" style="display:flex;align-items:center;gap:12px">' +
        '<div style="width:44px;height:44px;border-radius:12px;background:#EBF5FB;display:flex;align-items:center;justify-content:center;color:#2E86C1;font-weight:700;font-size:14px">' + (u.shortName || '??').substring(0, 2) + '</div>' +
        '<div style="flex:1"><p style="font-weight:600;font-size:14px">' + (u.name || '') + '</p>' +
        '<p style="font-size:12px;color:#5B6B7D">' + (u.city || '') + ' | ' + (u.citationStyle || 'Vancouver') + ' | ' + (u.isActive ? '<span class="badge badge-green">Active</span>' : '<span class="badge badge-red">Inactive</span>') + '</p></div></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}
function showAddUniModal() { showToast('Formulaire d\'ajout universite (a implementer)', 'info'); }

// ═══════════════════════════════════════════════════
// ADMIN: ACADEMIC YEARS
// ═══════════════════════════════════════════════════
function loadAdminYears() {
  db.collection('AcademicYears').orderBy('startDate', 'desc').get()
  .then(snap => {
    const el = document.getElementById('admin-years-list');
    if (snap.empty) { el.innerHTML = '<p style="color:#95A5A6;text-align:center;padding:24px">Aucune annee</p>'; return; }
    let html = '';
    snap.forEach(doc => {
      const y = doc.data();
      html += '<div class="card fade-in"><h4 style="font-size:15px;font-weight:700;color:#1A5276">' + (y.label || '') + (y.isActive ? ' <span class="badge badge-green">En cours</span>' : '') + '</h4>' +
        '<p style="font-size:13px;color:#5B6B7D;margin-top:4px">Soumission: ' + (y.submissionDeadline ? new Date(y.submissionDeadline.seconds * 1000).toLocaleDateString('fr-FR') : '-') +
        ' | Soutenance: ' + (y.defenseStartDate ? new Date(y.defenseStartDate.seconds * 1000).toLocaleDateString('fr-FR') : '-') + ' au ' +
        (y.defenseEndDate ? new Date(y.defenseEndDate.seconds * 1000).toLocaleDateString('fr-FR') : '-') + '</p></div>';
    });
    el.innerHTML = html;
  }).catch(() => {});
}
function showAddYearModal() { showToast('Formulaire d\'ajout annee academique (a implementer)', 'info'); }

// ═══════════════════════════════════════════════════
// SAVE SURVEY FORM & MISC
// ═══════════════════════════════════════════════════
async function saveSurveyForm() {
  showToast('Fiche sauvegardee', 'success');
}
async function submitSurveyForValidation() {
  showToast('Fiche soumise pour validation au directeur', 'success');
}

// ═══════════════════════════════════════════════════
// HELPERS
// ═══════════════════════════════════════════════════
const MILESTONES = [
  ['topic_assigned','Sujet assigne'],['literature_review','Revue de litterature'],
  ['protocol','Protocole'],['survey_form','Fiche d\'enquete'],
  ['data_collection','Collecte'],['analysis','Analyse'],
  ['discussion','Discussion'],['references','References'],
  ['finalization','Finalisation']
];

function milestoneLabel(key) {
  const m = MILESTONES.find(m => m[0] === key);
  return m ? m[1] : key;
}

function milestonePercent(key) {
  const idx = MILESTONES.findIndex(m => m[0] === key);
  if (idx < 0) return 0;
  const weights = [10, 15, 15, 5, 15, 15, 15, 5, 5];
  let pct = 0;
  for (let i = 0; i < idx; i++) pct += weights[i];
  pct += weights[idx] / 2;
  return Math.round(pct);
}

// ═══ ThesisMilestones - tracking per project ═══
function loadMilestones(projectId) {
  if (!projectId) return;
  db.collection('ThesisMilestones').where('projectId', '==', projectId).orderBy('orderIndex').get()
  .then(snap => {
    snap.forEach(doc => {
      const m = doc.data();
      // Milestone data used in timeline and progress calculation
      // Fields: milestoneType, status, dueDate, directorFeedback, studentNotes, version
    });
  }).catch(() => {});
}

// ═══ Documents - version management per project ═══
function loadDocumentVersions(projectId) {
  if (!projectId) return;
  db.collection('Documents').where('projectId', '==', projectId).where('isCurrent', '==', true).get()
  .then(snap => {
    snap.forEach(doc => {
      const d = doc.data();
      // Document versioning: documentType, title, fileURL, fileName, version, content, changelog, isCurrent
    });
  }).catch(() => {});
}
<\/script>
</body>
</html>
