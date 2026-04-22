<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>EATLAOS</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700;800&family=Instrument+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js">
// ═══ NEW ENTRY FUNCTIONS ═══
function goCustomerLogin(){ showPage('customerLoginPage'); }
function goStaffLogin(){ showPage('staffLoginPage'); }

function switchCL(tab, btn){
  document.querySelectorAll('.cl-tab').forEach(t=>t.classList.remove('active'));
  btn.classList.add('active');
  document.getElementById('clLoginForm').style.display = tab==='login'?'block':'none';
  document.getElementById('clRegForm').style.display = tab==='reg'?'block':'none';
}

function doCustLogin(){
  const u=document.getElementById('clUser').value.trim();
  if(!u){document.getElementById('clLoginErr').textContent='Please enter email or username';return;}
  let c=DB.customers.find(x=>x.email===u||x.name===u);
  if(!c){c={id:'c'+Date.now(),name:u,email:u,joined:new Date().toLocaleDateString(),orders:0,spent:0};DB.customers.push(c);saveDB();}
  enterCustApp(c);
}

function doCustGuest(){
  const c={id:'g'+Date.now(),name:'Guest',email:'guest',joined:new Date().toLocaleDateString(),orders:0,spent:0};
  enterCustApp(c);
}

function doCustReg(){
  const name=document.getElementById('clRegName').value.trim();
  const email=document.getElementById('clRegEmail').value.trim();
  if(!name||!email){document.getElementById('clRegErr').textContent='Please fill name and email';return;}
  if(DB.customers.find(c=>c.email===email)){document.getElementById('clRegErr').textContent='Email already exists';return;}
  const c={id:'c'+Date.now(),name,email,joined:new Date().toLocaleDateString(),orders:0,spent:0};
  DB.customers.push(c);saveDB();
  enterCustApp(c);
}

function enterCustApp(c){
  currentUser={name:c.name,role:'customer',custId:c.id};
  document.getElementById('custNameSidebar').textContent=c.name;
  showPage('customerPage');
  custPanel('home',document.querySelector('#customerPage .nav-item'));
  renderFeaturedRestos();renderAllRestos();
}

</script>
<style>
:root{
  --bg:#0d0d0d; --surface:#161616; --surface2:#1e1e1e; --border:#2a2a2a;
  --white:#f0ede8; --gray:#888; --gray2:#555;
  --gold:#e8b84b; --gold2:#f5d07a; --gold-dim:rgba(232,184,75,0.12);
  --green:#2ecc71; --red:#e74c3c; --blue:#3498db; --orange:#f39c12;
  --radius:0px;
  --font-head:'Syne',sans-serif;
  --font-body:'Instrument Sans',sans-serif;
}
*{margin:0;padding:0;box-sizing:border-box;}
body{font-family:var(--font-body);background:var(--bg);color:var(--white);min-height:100vh;overflow-x:hidden;}

/* ── PAGES ── */
.page{display:none;min-height:100vh;}
.page.active{display:block;}
#staffLoginPage.active{display:flex;}

/* ── ANIMATIONS ── */
@keyframes fadeUp{from{opacity:0;transform:translateY(20px)}to{opacity:1;transform:translateY(0)}}
@keyframes fadeIn{from{opacity:0}to{opacity:1}}
@keyframes slideRight{from{opacity:0;transform:translateX(-20px)}to{opacity:1;transform:translateX(0)}}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.5}}
.anim-up{animation:fadeUp .5s ease forwards;}
.anim-fade{animation:fadeIn .4s ease forwards;}

/* ══════════ LOGIN ══════════ */
#loginPage{
  background:var(--bg);align-items:center;justify-content:center;
  min-height:100vh;position:relative;overflow:hidden;
}
.login-bg-grid{
  position:absolute;inset:0;
  background-image:linear-gradient(rgba(232,184,75,.04) 1px,transparent 1px),linear-gradient(90deg,rgba(232,184,75,.04) 1px,transparent 1px);
  background-size:40px 40px;
}
.login-bg-glow{position:absolute;width:600px;height:600px;border-radius:50%;background:radial-gradient(circle,rgba(232,184,75,.08) 0%,transparent 70%);top:50%;left:50%;transform:translate(-50%,-50%);}
.login-wrap{position:relative;display:grid;grid-template-columns:1fr 1fr;width:900px;max-width:calc(100vw - 32px);min-height:560px;border:1px solid var(--border);background:var(--surface);animation:fadeUp .6s ease;}
.login-left{padding:60px 48px;background:linear-gradient(135deg,#1a1a1a 0%,var(--surface) 100%);display:flex;flex-direction:column;justify-content:space-between;}
.login-brand{font-family:var(--font-head);font-size:36px;font-weight:800;letter-spacing:-1px;}
.login-brand span{color:var(--gold);}
.login-tagline{font-size:12px;letter-spacing:3px;text-transform:uppercase;color:var(--gray);margin-top:6px;}
.login-roles{margin-top:auto;}
.role-card{padding:16px 18px;border:1px solid var(--border);margin-bottom:10px;cursor:pointer;transition:all .25s;display:flex;align-items:center;gap:14px;}
.role-card:hover,.role-card.selected{border-color:var(--gold);background:var(--gold-dim);}
.role-icon{font-size:24px;width:40px;text-align:center;}
.role-name{font-family:var(--font-head);font-size:14px;font-weight:600;}
.role-desc{font-size:11px;color:var(--gray);margin-top:2px;}
.login-right{padding:60px 48px;display:flex;flex-direction:column;justify-content:center;}
.login-form-title{font-family:var(--font-head);font-size:28px;font-weight:700;margin-bottom:6px;}
.login-form-sub{font-size:13px;color:var(--gray);margin-bottom:32px;}
.fl-group{margin-bottom:16px;}
.fl-label{display:block;font-size:10px;letter-spacing:2px;text-transform:uppercase;color:var(--gray);margin-bottom:8px;}
.fl-input{width:100%;padding:13px 15px;background:var(--surface2);border:1px solid var(--border);color:var(--white);font-family:var(--font-body);font-size:14px;outline:none;transition:border-color .2s;}
.fl-input:focus{border-color:var(--gold);}
.fl-input::placeholder{color:var(--gray2);}
.btn-login{width:100%;padding:15px;background:var(--gold);border:none;color:#000;font-family:var(--font-head);font-size:13px;font-weight:700;letter-spacing:2px;text-transform:uppercase;cursor:pointer;transition:all .25s;margin-top:4px;}
.btn-login:hover{background:var(--gold2);}
.login-hint{font-size:11px;color:var(--gray2);margin-top:12px;text-align:center;}
.login-hint b{color:var(--gray);}
.login-error{color:var(--red);font-size:12px;margin-top:8px;min-height:16px;}

/* ══════════ LAYOUT SHELL ══════════ */
.shell{display:grid;grid-template-columns:220px 1fr;min-height:100vh;}
.sidebar{background:var(--surface);border-right:1px solid var(--border);position:sticky;top:0;height:100vh;overflow-y:auto;display:flex;flex-direction:column;}
.sidebar-brand{padding:24px 20px;border-bottom:1px solid var(--border);}
.sidebar-brand-name{font-family:var(--font-head);font-size:18px;font-weight:800;letter-spacing:-0.5px;}
.sidebar-brand-name span{color:var(--gold);}
.sidebar-role-badge{display:inline-block;padding:3px 10px;font-size:9px;letter-spacing:2px;text-transform:uppercase;margin-top:6px;font-weight:600;}
.badge-super{background:rgba(232,184,75,.2);color:var(--gold);}
.badge-owner{background:rgba(46,204,113,.15);color:var(--green);}
.badge-customer{background:rgba(52,152,219,.15);color:var(--blue);}
.sidebar-nav{padding:12px 0;flex:1;}
.nav-section{padding:6px 20px;font-size:9px;letter-spacing:3px;text-transform:uppercase;color:var(--gray2);margin-top:10px;}
.nav-item{display:flex;align-items:center;gap:12px;padding:11px 20px;color:var(--gray);cursor:pointer;transition:all .2s;font-size:13px;border:none;background:none;width:100%;text-align:left;font-family:var(--font-body);border-left:2px solid transparent;position:relative;}
.nav-item:hover{color:var(--white);background:rgba(255,255,255,.03);}
.nav-item.active{color:var(--gold);background:var(--gold-dim);border-left-color:var(--gold);}
.nav-icon{font-size:16px;width:20px;text-align:center;}
.nav-badge{margin-left:auto;background:var(--red);color:white;font-size:9px;font-weight:700;padding:2px 7px;border-radius:10px;}
.sidebar-footer{padding:16px 20px;border-top:1px solid var(--border);}
.sidebar-user{display:flex;align-items:center;gap:10px;}
.sidebar-avatar{width:32px;height:32px;background:var(--gold-dim);border:1px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:14px;border-radius:50%;}
.sidebar-username{font-size:13px;font-weight:500;}
.sidebar-userrole{font-size:10px;color:var(--gray);margin-top:1px;}
.logout-btn{margin-left:auto;background:none;border:1px solid var(--border);color:var(--gray);padding:5px 10px;cursor:pointer;font-size:11px;font-family:var(--font-body);transition:all .2s;}
.logout-btn:hover{border-color:var(--red);color:var(--red);}

/* ══════════ MAIN CONTENT ══════════ */
.main{background:#0f0f0f;min-height:100vh;overflow-x:hidden;}
.topbar{background:var(--surface);border-bottom:1px solid var(--border);padding:16px 32px;display:flex;justify-content:space-between;align-items:center;position:sticky;top:0;z-index:50;}
.topbar-title{font-family:var(--font-head);font-size:22px;font-weight:700;}
.topbar-right{display:flex;align-items:center;gap:12px;}
.topbar-btn{padding:8px 18px;border:1px solid var(--border);background:none;color:var(--gray);font-family:var(--font-body);font-size:11px;letter-spacing:1px;cursor:pointer;transition:all .2s;}
.topbar-btn:hover{border-color:var(--gold);color:var(--gold);}
.topbar-btn.primary{background:var(--gold);border-color:var(--gold);color:#000;font-weight:600;}
.topbar-btn.primary:hover{background:var(--gold2);}
.content-body{padding:28px 32px;}

/* ══════════ PANELS ══════════ */
.panel{display:none;}
.panel.active{display:block;animation:fadeUp .4s ease;}

/* ══════════ STATS ══════════ */
.stats-row{display:grid;gap:1px;background:var(--border);margin-bottom:24px;}
.stats-row.cols4{grid-template-columns:repeat(4,1fr);}
.stats-row.cols3{grid-template-columns:repeat(3,1fr);}
.stats-row.cols2{grid-template-columns:repeat(2,1fr);}
.stat-box{background:var(--surface);padding:24px 20px;position:relative;overflow:hidden;}
.stat-box::after{content:'';position:absolute;top:0;left:0;right:0;height:2px;background:var(--gold);}
.stat-label{font-size:10px;letter-spacing:2px;text-transform:uppercase;color:var(--gray);margin-bottom:10px;}
.stat-value{font-family:var(--font-head);font-size:32px;font-weight:800;line-height:1;}
.stat-change{font-size:11px;color:var(--green);margin-top:6px;}
.stat-icon{position:absolute;right:20px;top:20px;font-size:28px;opacity:.3;}

/* ══════════ CARDS ══════════ */
.card{background:var(--surface);border:1px solid var(--border);margin-bottom:20px;}
.card-head{padding:18px 22px;border-bottom:1px solid var(--border);display:flex;justify-content:space-between;align-items:center;gap:12px;flex-wrap:wrap;}
.card-title{font-family:var(--font-head);font-size:16px;font-weight:700;}
.card-body{padding:22px;}

/* ══════════ TABLE ══════════ */
.tbl-wrap{overflow-x:auto;}
table{width:100%;border-collapse:collapse;}
th{padding:10px 16px;text-align:left;font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--gray);border-bottom:1px solid var(--border);font-weight:500;white-space:nowrap;}
td{padding:13px 16px;font-size:13px;border-bottom:1px solid rgba(255,255,255,.04);vertical-align:middle;}
tr:last-child td{border-bottom:none;}
tr:hover td{background:rgba(255,255,255,.02);}
.td-name{font-weight:500;}
.td-sub{font-size:11px;color:var(--gray);margin-top:2px;}

/* ══════════ BADGES ══════════ */
.tag{display:inline-block;padding:3px 10px;font-size:10px;letter-spacing:1px;text-transform:uppercase;font-weight:600;}
.tag-green{background:rgba(46,204,113,.15);color:var(--green);}
.tag-red{background:rgba(231,76,60,.15);color:var(--red);}
.tag-gold{background:var(--gold-dim);color:var(--gold);}
.tag-blue{background:rgba(52,152,219,.15);color:var(--blue);}
.tag-gray{background:rgba(136,136,136,.15);color:var(--gray);}
.tag-orange{background:rgba(243,156,18,.15);color:var(--orange);}

/* ══════════ BUTTONS ══════════ */
.btn{padding:8px 18px;border:1px solid var(--border);background:none;color:var(--gray);font-family:var(--font-body);font-size:11px;letter-spacing:1px;cursor:pointer;transition:all .2s;}
.btn:hover{border-color:var(--white);color:var(--white);}
.btn-gold{background:var(--gold);border-color:var(--gold);color:#000;font-weight:600;}
.btn-gold:hover{background:var(--gold2);border-color:var(--gold2);color:#000;}
.btn-red:hover{border-color:var(--red);color:var(--red);}
.btn-sm{padding:5px 12px;font-size:10px;}
.btn-icon{width:32px;height:32px;padding:0;display:inline-flex;align-items:center;justify-content:center;font-size:14px;}
.actions{display:flex;gap:6px;flex-wrap:wrap;}

/* ══════════ CHART ══════════ */
.chart-box{background:var(--surface);border:1px solid var(--border);padding:22px;margin-bottom:20px;}
.chart-box-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:18px;flex-wrap:wrap;gap:10px;}
.chart-box-title{font-family:var(--font-head);font-size:15px;font-weight:700;}
.chart-tabs{display:flex;gap:0;}
.chart-tab{padding:6px 14px;border:1px solid var(--border);border-right:none;background:none;color:var(--gray);font-family:var(--font-body);font-size:10px;letter-spacing:1px;cursor:pointer;transition:all .2s;}
.chart-tab:last-child{border-right:1px solid var(--border);}
.chart-tab.active{background:var(--gold);border-color:var(--gold);color:#000;font-weight:600;}
.chart-wrap{position:relative;height:220px;}

/* ══════════ MODAL ══════════ */
.modal-bg{display:none;position:fixed;inset:0;background:rgba(0,0,0,.75);z-index:400;backdrop-filter:blur(4px);align-items:center;justify-content:center;padding:20px;}
.modal-bg.open{display:flex;}
.modal-box{background:var(--surface);border:1px solid var(--border);width:520px;max-width:100%;max-height:90vh;overflow-y:auto;animation:fadeUp .3s ease;}
.modal-box.wide{width:680px;}
.modal-head{padding:22px 26px;border-bottom:1px solid var(--border);display:flex;justify-content:space-between;align-items:center;}
.modal-title{font-family:var(--font-head);font-size:20px;font-weight:700;}
.modal-close{background:none;border:1px solid var(--border);color:var(--gray);width:32px;height:32px;cursor:pointer;font-size:16px;display:flex;align-items:center;justify-content:center;transition:all .2s;}
.modal-close:hover{background:var(--red);border-color:var(--red);color:white;}
.modal-body{padding:26px;}
.modal-foot{padding:16px 26px;border-top:1px solid var(--border);display:flex;justify-content:flex-end;gap:10px;}
.fg{margin-bottom:16px;}
.fg label{display:block;font-size:10px;letter-spacing:2px;text-transform:uppercase;color:var(--gray);margin-bottom:7px;}
.fi{width:100%;padding:11px 13px;background:var(--surface2);border:1px solid var(--border);color:var(--white);font-family:var(--font-body);font-size:13px;outline:none;transition:border-color .2s;}
.fi:focus{border-color:var(--gold);}
.fi::placeholder{color:var(--gray2);}
select.fi{cursor:pointer;}
.form-row{display:grid;grid-template-columns:1fr 1fr;gap:14px;}

/* ══════════ RESTAURANT CARD (customer view) ══════════ */
.resto-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(300px,1fr));gap:1px;background:var(--border);margin-bottom:24px;}
.resto-card{background:var(--surface);cursor:pointer;transition:all .3s;position:relative;overflow:hidden;}
.resto-card:hover{background:var(--surface2);}
.resto-card:hover .resto-img{transform:scale(1.04);}
.resto-img-wrap{overflow:hidden;height:160px;background:var(--surface2);}
.resto-img{width:100%;height:100%;display:flex;align-items:center;justify-content:center;font-size:60px;transition:transform .4s;}
.resto-body{padding:18px;}
.resto-name{font-family:var(--font-head);font-size:18px;font-weight:700;margin-bottom:4px;}
.resto-cuisine{font-size:11px;color:var(--gold);letter-spacing:2px;text-transform:uppercase;margin-bottom:8px;}
.resto-meta{display:flex;gap:14px;font-size:12px;color:var(--gray);}
.resto-status{position:absolute;top:12px;right:12px;}

/* ══════════ MENU GRID (customer) ══════════ */
.menu-grid-c{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:1px;background:var(--border);}
.menu-card-c{background:var(--surface);cursor:pointer;transition:background .2s;position:relative;}
.menu-card-c:hover{background:var(--surface2);}
.mci{width:100%;height:160px;display:flex;align-items:center;justify-content:center;font-size:52px;background:var(--surface2);}
.mcb{padding:16px;}
.mc-cat{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--gold);margin-bottom:5px;}
.mc-name{font-family:var(--font-head);font-size:16px;font-weight:700;margin-bottom:5px;}
.mc-desc{font-size:12px;color:var(--gray);line-height:1.6;margin-bottom:12px;}
.mc-foot{display:flex;align-items:center;justify-content:space-between;}
.mc-price{font-family:var(--font-head);font-size:20px;font-weight:700;color:var(--gold);}
.mc-add{width:36px;height:36px;background:var(--gold);border:none;color:#000;font-size:18px;cursor:pointer;display:flex;align-items:center;justify-content:center;transition:background .2s;}
.mc-add:hover{background:var(--gold2);}

/* ══════════ CART ══════════ */
.cart-fab{position:fixed;bottom:28px;right:28px;z-index:100;background:var(--gold);border:none;color:#000;padding:14px 24px;font-family:var(--font-head);font-size:13px;font-weight:700;cursor:pointer;display:flex;align-items:center;gap:10px;box-shadow:0 8px 32px rgba(232,184,75,.3);transition:all .3s;display:none;}
.cart-fab.visible{display:flex;}
.cart-fab:hover{background:var(--gold2);transform:translateY(-2px);}
.cart-fab-count{background:#000;color:var(--gold);width:22px;height:22px;border-radius:50%;font-size:11px;font-weight:800;display:flex;align-items:center;justify-content:center;}
.cart-overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.6);z-index:200;backdrop-filter:blur(4px);}
.cart-overlay.open{display:block;}
.cart-panel{position:fixed;right:-460px;top:0;bottom:0;width:420px;background:var(--surface);z-index:201;transition:right .35s cubic-bezier(.4,0,.2,1);display:flex;flex-direction:column;border-left:1px solid var(--border);}
.cart-panel.open{right:0;}
.cart-ph{padding:22px;border-bottom:1px solid var(--border);display:flex;justify-content:space-between;align-items:center;}
.cart-ptitle{font-family:var(--font-head);font-size:20px;font-weight:700;}
.cart-items{flex:1;overflow-y:auto;padding:16px 22px;}
.ci{display:flex;gap:12px;padding:12px 0;border-bottom:1px solid var(--border);animation:slideRight .3s ease;}
.ci-emoji{width:52px;height:52px;background:var(--surface2);display:flex;align-items:center;justify-content:center;font-size:22px;flex-shrink:0;}
.ci-info{flex:1;}
.ci-name{font-weight:600;font-size:14px;margin-bottom:3px;}
.ci-price{font-size:12px;color:var(--gold);}
.ci-qty{display:flex;align-items:center;gap:10px;margin-top:8px;}
.ci-qbtn{width:24px;height:24px;background:var(--surface2);border:none;color:var(--white);cursor:pointer;font-size:14px;display:flex;align-items:center;justify-content:center;transition:all .2s;}
.ci-qbtn:hover{background:var(--gold);color:#000;}
.ci-qnum{font-family:var(--font-head);font-size:16px;min-width:16px;text-align:center;}
.ci-remove{background:none;border:none;color:var(--gray2);cursor:pointer;font-size:15px;align-self:flex-start;padding:3px;transition:color .2s;}
.ci-remove:hover{color:var(--red);}
.cart-foot{padding:18px 22px;border-top:1px solid var(--border);background:var(--surface2);}
.cart-row{display:flex;justify-content:space-between;font-size:13px;color:var(--gray);margin-bottom:6px;}
.cart-total-row{display:flex;justify-content:space-between;padding-top:10px;border-top:1px solid var(--border);margin-bottom:16px;}
.cart-total-label{font-family:var(--font-head);font-size:18px;}
.cart-total-val{font-family:var(--font-head);font-size:22px;color:var(--gold);}
.btn-order{width:100%;padding:15px;background:var(--gold);border:none;color:#000;font-family:var(--font-head);font-size:13px;font-weight:700;letter-spacing:2px;text-transform:uppercase;cursor:pointer;transition:all .25s;}
.btn-order:hover{background:var(--gold2);}
.cart-empty-state{text-align:center;padding:60px 20px;color:var(--gray);}
.cart-resto-name{font-size:11px;color:var(--gold);letter-spacing:1px;margin-bottom:16px;padding:8px 16px;border:1px solid var(--border);text-align:center;text-transform:uppercase;}

/* ══════════ ITEM DETAIL ══════════ */
.detail-modal{width:500px;}
.detail-img{height:200px;background:var(--surface2);display:flex;align-items:center;justify-content:center;font-size:80px;}
.detail-body{padding:24px;}
.detail-name{font-family:var(--font-head);font-size:26px;font-weight:800;margin-bottom:8px;}
.detail-desc{font-size:14px;color:var(--gray);line-height:1.8;margin-bottom:20px;}
.detail-price{font-family:var(--font-head);font-size:32px;font-weight:800;color:var(--gold);}
.detail-running{font-size:12px;color:var(--gray);margin-top:4px;}
.qty-ctrl{display:flex;align-items:center;gap:14px;}
.qty-btn-lg{width:38px;height:38px;background:var(--surface2);border:1px solid var(--border);color:var(--white);cursor:pointer;font-size:18px;display:flex;align-items:center;justify-content:center;transition:all .2s;}
.qty-btn-lg:hover{background:var(--gold);border-color:var(--gold);color:#000;}
.qty-num-lg{font-family:var(--font-head);font-size:24px;font-weight:700;min-width:28px;text-align:center;}

/* ══════════ ORDERS (customer) ══════════ */
.order-card{background:var(--surface);border:1px solid var(--border);margin-bottom:14px;animation:fadeUp .4s ease;}
.order-head{padding:14px 18px;border-bottom:1px solid var(--border);display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:8px;}
.order-id{font-family:var(--font-head);font-size:16px;font-weight:700;}
.order-time{font-size:11px;color:var(--gray);}
.order-body{padding:14px 18px;}
.order-item-row{display:flex;justify-content:space-between;padding:5px 0;border-bottom:1px dashed var(--border);font-size:13px;}
.order-item-row:last-of-type{border-bottom:none;}
.order-total-line{display:flex;justify-content:space-between;padding-top:10px;font-family:var(--font-head);font-size:16px;}
.receipt-btn{background:none;border:1px solid var(--border);color:var(--gray);padding:6px 14px;font-size:11px;font-family:var(--font-body);cursor:pointer;margin-top:10px;transition:all .2s;}
.receipt-btn:hover{border-color:var(--gold);color:var(--gold);}

/* ══════════ NOTIFICATION ══════════ */
.notif{position:fixed;top:20px;right:20px;background:var(--surface);border:1px solid var(--border);border-left:3px solid var(--gold);color:var(--white);padding:14px 20px;z-index:999;font-size:13px;display:flex;align-items:center;gap:10px;transform:translateY(-80px);opacity:0;transition:all .35s cubic-bezier(.4,0,.2,1);min-width:260px;max-width:340px;}
.notif.show{transform:translateY(0);opacity:1;}
.notif-icon{font-size:18px;flex-shrink:0;}
.new-order-alert{position:fixed;top:20px;left:50%;transform:translateX(-50%) translateY(-80px);background:var(--green);color:#000;padding:12px 24px;z-index:998;font-family:var(--font-head);font-size:13px;font-weight:700;display:flex;align-items:center;gap:8px;transition:transform .4s ease,opacity .4s ease;opacity:0;}
.new-order-alert.show{transform:translateX(-50%) translateY(0);opacity:1;}

/* ══════════ RECEIPT ══════════ */
.receipt-paper{font-family:'Courier New',monospace;padding:24px;max-width:300px;margin:0 auto;color:#000;background:#fff;}
.rp-logo{text-align:center;font-size:18px;font-weight:900;letter-spacing:4px;margin-bottom:2px;}
.rp-sub{text-align:center;font-size:10px;margin-bottom:14px;color:#555;}
.rp-div{border:none;border-top:1px dashed #aaa;margin:10px 0;}
.rp-row{display:flex;justify-content:space-between;font-size:12px;margin:3px 0;}
.rp-total{display:flex;justify-content:space-between;font-size:14px;font-weight:900;margin:3px 0;}
.rp-foot{text-align:center;font-size:10px;margin-top:14px;color:#777;}
#printArea{display:none;}

/* ══════════ COMMISSION BADGE ══════════ */
.commission-badge{display:inline-flex;align-items:center;gap:6px;padding:4px 12px;background:var(--gold-dim);border:1px solid rgba(232,184,75,.3);font-size:11px;color:var(--gold);}

/* ══════════ MOBILE ══════════ */
.mobile-bar{display:none;position:fixed;bottom:0;left:0;right:0;z-index:150;background:var(--surface);border-top:1px solid var(--border);}
.mobile-bar-inner{display:flex;justify-content:space-around;padding:6px 0 env(safe-area-inset-bottom);}
.mb-btn{background:none;border:none;display:flex;flex-direction:column;align-items:center;gap:3px;padding:6px 14px;cursor:pointer;color:var(--gray);font-family:var(--font-body);font-size:9px;letter-spacing:1px;text-transform:uppercase;transition:color .2s;}
.mb-btn.active{color:var(--gold);}
.mb-icon{font-size:18px;}

@media(max-width:900px){
  .login-wrap{grid-template-columns:1fr;}
  .login-left{padding:32px 28px;}
  .login-right{padding:32px 28px;}
  .login-roles{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-top:24px;}
}
@media(max-width:768px){
  .shell{grid-template-columns:1fr;}
  .sidebar{display:none;}
  .mobile-bar{display:block;}
  body{padding-bottom:70px;}
  .content-body{padding:16px;}
  .topbar{padding:12px 16px;}
  .stats-row.cols4{grid-template-columns:1fr 1fr;}
  .stats-row.cols3{grid-template-columns:1fr 1fr;}
  .cart-panel{width:100%;right:-100%;}
  .resto-grid{grid-template-columns:1fr 1fr;}
  .menu-grid-c{grid-template-columns:1fr 1fr;}
  .form-row{grid-template-columns:1fr;}
}
@media(max-width:480px){
  .stats-row.cols4,.stats-row.cols3,.stats-row.cols2{grid-template-columns:1fr;}
  .resto-grid{grid-template-columns:1fr;}
  .menu-grid-c{grid-template-columns:1fr;}
}
::-webkit-scrollbar{width:4px;}
::-webkit-scrollbar-track{background:transparent;}
::-webkit-scrollbar-thumb{background:var(--border);}
</style>
</head>
<body>

<!-- LANDING PAGE -->
<div id="landingPage" class="page active">
  <style>
  #landingPage{display:none;background:#0d0d0d;align-items:center;justify-content:center;min-height:100vh;position:relative;overflow:hidden;flex-direction:column;}
  #landingPage.active{display:flex;}
  .land-grid{position:absolute;inset:0;background-image:linear-gradient(rgba(232,184,75,.03) 1px,transparent 1px),linear-gradient(90deg,rgba(232,184,75,.03) 1px,transparent 1px);background-size:48px 48px;}
  .land-glow{position:absolute;width:700px;height:700px;border-radius:50%;background:radial-gradient(circle,rgba(232,184,75,.07) 0%,transparent 65%);top:50%;left:50%;transform:translate(-50%,-50%);}
  .land-content{position:relative;text-align:center;padding:40px 20px;animation:fadeUp .8s ease;}
  .land-logo{font-family:var(--font-head);font-size:80px;font-weight:800;letter-spacing:-4px;line-height:1;}
  .land-logo span{color:var(--gold);}
  .land-tagline{font-size:11px;letter-spacing:5px;text-transform:uppercase;color:var(--gray);margin-top:8px;margin-bottom:60px;}
  .land-cards{display:grid;grid-template-columns:1fr 1fr;gap:20px;max-width:560px;margin:0 auto 36px;}
  .land-card{padding:36px 28px;border:1px solid var(--border);background:var(--surface);cursor:pointer;transition:all .3s;text-align:left;position:relative;overflow:hidden;}
  .land-card:hover{border-color:var(--gold);transform:translateY(-6px);box-shadow:0 20px 60px rgba(232,184,75,.12);}
  .land-card-icon{font-size:44px;display:block;margin-bottom:16px;}
  .land-card-title{font-family:var(--font-head);font-size:22px;font-weight:700;margin-bottom:8px;}
  .land-card-desc{font-size:13px;color:var(--gray);line-height:1.7;}
  .land-card-arrow{margin-top:20px;font-size:20px;color:var(--gold);}
  @media(max-width:600px){.land-cards{grid-template-columns:1fr;}.land-logo{font-size:52px;}}
  </style>
  <div class="land-grid"></div>
  <div class="land-glow"></div>
  <div class="land-content">
    <div class="land-logo">EAT<span>LAOS</span></div>
    <div class="land-tagline">Multi-Restaurant Food Platform · Vientiane</div>
    <div class="land-cards">
      <div class="land-card" onclick="goCustomerLogin()">
        <span class="land-card-icon">🍽️</span>
        <div class="land-card-title">Order Food</div>
        <div class="land-card-desc">Browse restaurants, order delicious food and track your delivery</div>
        <div class="land-card-arrow">→ Customer Login</div>
      </div>
      <div class="land-card" onclick="goStaffLogin()">
        <span class="land-card-icon">⚙️</span>
        <div class="land-card-title">Staff Access</div>
        <div class="land-card-desc">Super Admin &amp; Restaurant Owner management portal</div>
        <div class="land-card-arrow">→ Staff Login</div>
      </div>
    </div>
  </div>
</div>

<!-- CUSTOMER LOGIN (beautiful warm style) -->
<div id="customerLoginPage" class="page">
  <style>
  #customerLoginPage{display:none;min-height:100vh;align-items:center;justify-content:center;background:#f7f3ee;position:relative;overflow:hidden;}
  #customerLoginPage.active{display:flex;}
  .cl-bg-left{position:absolute;left:0;top:0;bottom:0;width:46%;background:linear-gradient(160deg,#1a0e05,#2d1a0a);}
  .cl-glow{position:absolute;left:100px;top:50%;transform:translateY(-50%);width:400px;height:400px;border-radius:50%;background:radial-gradient(circle,rgba(232,184,75,.15) 0%,transparent 70%);}
  .cl-wrap{position:relative;display:grid;grid-template-columns:1fr 1fr;width:940px;max-width:calc(100vw - 24px);min-height:580px;box-shadow:0 40px 120px rgba(0,0,0,.3);animation:fadeUp .6s ease;}
  .cl-left-panel{padding:56px 48px;background:linear-gradient(160deg,#1a0e05,#2d1a0a);display:flex;flex-direction:column;justify-content:space-between;position:relative;overflow:hidden;}
  .cl-back{background:none;border:1px solid rgba(255,255,255,.15);color:rgba(255,255,255,.5);padding:6px 14px;font-family:var(--font-body);font-size:11px;letter-spacing:1px;cursor:pointer;transition:all .2s;align-self:flex-start;}
  .cl-back:hover{border-color:var(--gold);color:var(--gold);}
  .cl-food-big{font-size:80px;display:block;margin-bottom:20px;animation:float 3s ease infinite;}
  @keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-12px)}}
  .cl-hero-title{font-size:40px;font-weight:700;color:#fff;line-height:1.2;margin-bottom:12px;font-family:'Cormorant Garamond',serif;}
  .cl-hero-title em{color:var(--gold);font-style:italic;}
  .cl-hero-desc{font-size:13px;color:rgba(255,255,255,.45);line-height:1.9;}
  .cl-feats{display:flex;flex-direction:column;gap:10px;}
  .cl-feat{font-size:12px;color:rgba(255,255,255,.55);display:flex;align-items:center;gap:8px;}
  .cl-right-panel{background:#fff;padding:52px 48px;display:flex;flex-direction:column;justify-content:center;}
  .cl-logo{font-family:var(--font-head);font-size:20px;font-weight:800;letter-spacing:-0.5px;color:#1a1a1a;margin-bottom:28px;}
  .cl-logo span{color:#e8b84b;}
  .cl-welcome{font-size:28px;font-weight:800;font-family:var(--font-head);color:#1a1a1a;margin-bottom:4px;}
  .cl-sub{font-size:13px;color:#999;margin-bottom:24px;}
  .cl-tabs{display:flex;border-bottom:2px solid #f0ede8;margin-bottom:24px;}
  .cl-tab{padding:9px 18px;background:none;border:none;font-family:var(--font-body);font-size:12px;color:#aaa;cursor:pointer;transition:all .2s;position:relative;font-weight:500;}
  .cl-tab.active{color:#1a1a1a;}
  .cl-tab.active::after{content:'';position:absolute;bottom:-2px;left:0;right:0;height:2px;background:#e8b84b;}
  .cl-ig{margin-bottom:14px;}
  .cl-lbl{display:block;font-size:10px;letter-spacing:2px;text-transform:uppercase;color:#aaa;margin-bottom:7px;font-weight:500;}
  .cl-inp{width:100%;padding:12px 14px;background:#f8f6f3;border:1.5px solid #ede9e3;color:#1a1a1a;font-family:var(--font-body);font-size:14px;outline:none;transition:border-color .2s;}
  .cl-inp:focus{border-color:#e8b84b;background:#fff;}
  .cl-inp::placeholder{color:#ccc;}
  .cl-btn{width:100%;padding:14px;background:#1a1a1a;border:none;color:#fff;font-family:var(--font-head);font-size:13px;font-weight:700;letter-spacing:2px;text-transform:uppercase;cursor:pointer;transition:all .25s;margin-top:6px;}
  .cl-btn:hover{background:#e8b84b;color:#000;}
  .cl-or{display:flex;align-items:center;gap:10px;margin:14px 0;color:#ccc;font-size:12px;}
  .cl-or::before,.cl-or::after{content:'';flex:1;height:1px;background:#ede9e3;}
  .cl-guest{width:100%;padding:12px;background:#fff;border:1.5px solid #ede9e3;color:#666;font-family:var(--font-body);font-size:13px;cursor:pointer;transition:all .25s;font-weight:500;}
  .cl-guest:hover{border-color:#e8b84b;color:#1a1a1a;}
  .cl-err{color:#e74c3c;font-size:12px;margin-top:8px;min-height:16px;}
  @media(max-width:820px){.cl-wrap{grid-template-columns:1fr;}.cl-left-panel{min-height:auto;padding:32px 28px;}.cl-right-panel{padding:32px 28px;}}
  </style>
  <div class="cl-bg-left"></div>
  <div class="cl-glow"></div>
  <div class="cl-wrap">
    <div class="cl-left-panel">
      <button class="cl-back" onclick="showPage('landingPage')">← Back</button>
      <div>
        <span class="cl-food-big">🍜</span>
        <div class="cl-hero-title">Delicious food,<br><em>delivered fast</em></div>
        <div class="cl-hero-desc">Order from the best restaurants in Vientiane. Fresh, fast and always satisfying.</div>
      </div>
      <div class="cl-feats">
        <div class="cl-feat">⚡ Fast delivery across Vientiane</div>
        <div class="cl-feat">🏪 Multiple restaurants in one place</div>
        <div class="cl-feat">📋 Track every order in real time</div>
      </div>
    </div>
    <div class="cl-right-panel">
      <div class="cl-logo">EAT<span>LAOS</span></div>
      <div class="cl-welcome">Welcome back 👋</div>
      <div class="cl-sub">Sign in to order your favourite food</div>
      <div class="cl-tabs">
        <button class="cl-tab active" onclick="switchCL('login',this)">Sign In</button>
        <button class="cl-tab" onclick="switchCL('reg',this)">Register</button>
      </div>
      <div id="clLoginForm">
        <div class="cl-ig"><label class="cl-lbl">Email or Username</label><input class="cl-inp" id="clUser" placeholder="your@email.com" onkeydown="if(event.key==='Enter') doCustLogin()"></div>
        <div class="cl-ig"><label class="cl-lbl">Password</label><input class="cl-inp" type="password" id="clPass" placeholder="••••••••" onkeydown="if(event.key==='Enter') doCustLogin()"></div>
        <button class="cl-btn" onclick="doCustLogin()">Sign In & Order →</button>
        <div class="cl-err" id="clLoginErr"></div>
        <div class="cl-or">or</div>
        <button class="cl-guest" onclick="doCustGuest()">👤 Continue as Guest</button>
      </div>
      <div id="clRegForm" style="display:none">
        <div class="cl-ig"><label class="cl-lbl">Full Name</label><input class="cl-inp" id="clRegName" placeholder="Your full name"></div>
        <div class="cl-ig"><label class="cl-lbl">Email</label><input class="cl-inp" id="clRegEmail" placeholder="your@email.com"></div>
        <div class="cl-ig"><label class="cl-lbl">Password</label><input class="cl-inp" type="password" id="clRegPass" placeholder="Create a password"></div>
        <button class="cl-btn" onclick="doCustReg()">Create Account →</button>
        <div class="cl-err" id="clRegErr"></div>
      </div>
    </div>
  </div>
</div>


<!-- ══════════════════════════════
     LOGIN
══════════════════════════════ -->
<div id="staffLoginPage" class="page active">
  <div class="login-bg-grid"></div>
  <div class="login-bg-glow"></div>
  <div class="login-wrap">
    <div class="login-left">
      <div>
        <div class="login-brand">EAT<span>LAOS</span></div>
        <div class="login-tagline">Staff Portal</div>
      </div>
      <div class="login-roles">
        <div class="role-card selected" onclick="selectRole('superadmin',this)">
          <div class="role-icon">👑</div>
          <div><div class="role-name">Super Admin</div><div class="role-desc">Platform owner — full control</div></div>
        </div>
        <div class="role-card" onclick="selectRole('owner',this)">
          <div class="role-icon">🏪</div>
          <div><div class="role-name">Restaurant Owner</div><div class="role-desc">Manage your restaurant</div></div>
        </div>

      </div>
    </div>
    <div class="login-right">
      <div class="login-form-title" id="loginTitle">Super Admin Login</div>
      <div class="login-form-sub" id="loginSub">Access the full platform control panel</div>
      <div class="fl-group">
        <label class="fl-label">Username / Email</label>
        <input class="fl-input" id="loginUser" placeholder="Enter username">
      </div>
      <div class="fl-group">
        <label class="fl-label">Password</label>
        <input class="fl-input" type="password" id="loginPass" placeholder="••••••••">
      </div>
      <button class="btn-login" onclick="doLogin()">Sign In →</button>
      <div class="login-error" id="loginError"></div>
      <div class="login-hint" id="loginHint">Use <b>superadmin</b> / <b>admin123</b></div>
    </div>
  </div>
</div>

<!-- ══════════════════════════════
     SUPER ADMIN SHELL
══════════════════════════════ -->
<div id="superAdminPage" class="page">
  <div class="shell">
    <aside class="sidebar">
      <div class="sidebar-brand">
        <div class="sidebar-brand-name">EAT<span>LAOS</span></div>
        <div class="sidebar-role-badge badge-super">👑 Super Admin</div>
      </div>
      <nav class="sidebar-nav">
        <div class="nav-section">Overview</div>
        <button class="nav-item active" onclick="saPanel('dashboard',this)"><span class="nav-icon">📊</span>Dashboard</button>
        <button class="nav-item" onclick="saPanel('revenue',this)"><span class="nav-icon">💰</span>Revenue & Commission</button>
        <div class="nav-section">Management</div>
        <button class="nav-item" onclick="saPanel('restaurants',this)"><span class="nav-icon">🏪</span>Restaurants<span class="nav-badge" id="pendingBadge">0</span></button>
        <button class="nav-item" onclick="saPanel('allorders',this)"><span class="nav-icon">🧾</span>All Orders</button>
        <button class="nav-item" onclick="saPanel('customers',this)"><span class="nav-icon">👥</span>Customers</button>
        <div class="nav-section">System</div>
        <button class="nav-item" onclick="saPanel('settings',this)"><span class="nav-icon">⚙️</span>Platform Settings</button>
      </nav>
      <div class="sidebar-footer">
        <div class="sidebar-user">
          <div class="sidebar-avatar">👑</div>
          <div><div class="sidebar-username">Super Admin</div><div class="sidebar-userrole">Platform Owner</div></div>
          <button class="logout-btn" onclick="logout()">Out</button>
        </div>
      </div>
    </aside>
    <main class="main">
      <div class="topbar">
        <div class="topbar-title" id="saTitleBar">Dashboard</div>
        <div class="topbar-right">
          <button class="topbar-btn primary" onclick="openModal('addRestoModal')">+ Add Restaurant</button>
          <button class="topbar-btn" id="saSoundBtn" onclick="toggleSound()">🔔 Sound ON</button>
        </div>
      </div>
      <div class="content-body">

        <!-- DASHBOARD -->
        <div class="panel active" id="sa-dashboard">
          <div class="stats-row cols4">
            <div class="stat-box"><div class="stat-icon">🏪</div><div class="stat-label">Total Restaurants</div><div class="stat-value" id="sa-stat-restos">0</div><div class="stat-change">↑ Active listings</div></div>
            <div class="stat-box"><div class="stat-icon">🧾</div><div class="stat-label">Total Orders</div><div class="stat-value" id="sa-stat-orders">0</div><div class="stat-change">↑ All time</div></div>
            <div class="stat-box"><div class="stat-icon">💰</div><div class="stat-label">Platform Revenue</div><div class="stat-value" id="sa-stat-rev">$0</div><div class="stat-change">Your commission</div></div>
            <div class="stat-box"><div class="stat-icon">👥</div><div class="stat-label">Customers</div><div class="stat-value" id="sa-stat-cust">0</div><div class="stat-change">Registered users</div></div>
          </div>
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:20px;margin-bottom:20px;">
            <div class="chart-box">
              <div class="chart-box-head"><div class="chart-box-title">Platform Revenue</div>
                <div class="chart-tabs"><button class="chart-tab active" onclick="saChart('week',this)">Week</button><button class="chart-tab" onclick="saChart('month',this)">Month</button></div>
              </div>
              <div class="chart-wrap"><canvas id="saRevenueChart"></canvas></div>
            </div>
            <div class="chart-box">
              <div class="chart-box-head"><div class="chart-box-title">Orders by Restaurant</div></div>
              <div class="chart-wrap"><canvas id="saRestoChart"></canvas></div>
            </div>
          </div>
          <div class="card">
            <div class="card-head"><div class="card-title">Recent Orders — All Restaurants</div></div>
            <div class="tbl-wrap"><table><thead><tr><th>Order</th><th>Customer</th><th>Restaurant</th><th>Total</th><th>Commission</th><th>Status</th></tr></thead><tbody id="sa-recent-orders"></tbody></table></div>
          </div>
        </div>

        <!-- REVENUE -->
        <div class="panel" id="sa-revenue">
          <div class="stats-row cols3">
            <div class="stat-box"><div class="stat-label">Gross Revenue (All)</div><div class="stat-value" id="sa-gross">$0</div><div class="stat-change">Customer payments</div></div>
            <div class="stat-box"><div class="stat-icon">👑</div><div class="stat-label">Your Commission</div><div class="stat-value" id="sa-commission">$0</div><div class="stat-change" id="sa-comm-pct">At 15%</div></div>
            <div class="stat-box"><div class="stat-label">Paid to Restaurants</div><div class="stat-value" id="sa-paid-out">$0</div><div class="stat-change">85% of gross</div></div>
          </div>
          <div class="card">
            <div class="card-head"><div class="card-title">Revenue by Restaurant</div></div>
            <div class="tbl-wrap"><table><thead><tr><th>Restaurant</th><th>Total Orders</th><th>Gross Revenue</th><th>Commission %</th><th>Your Earnings</th><th>Restaurant Earnings</th></tr></thead><tbody id="sa-revenue-table"></tbody></table></div>
          </div>
        </div>

        <!-- RESTAURANTS -->
        <div class="panel" id="sa-restaurants">
          <div class="card">
            <div class="card-head">
              <div class="card-title">All Restaurants</div>
              <button class="btn btn-gold" onclick="openModal('addRestoModal')">+ Add Restaurant</button>
            </div>
            <div class="tbl-wrap"><table><thead><tr><th>Restaurant</th><th>Owner</th><th>Cuisine</th><th>Commission</th><th>Status</th><th>Orders</th><th>Actions</th></tr></thead><tbody id="sa-restos-table"></tbody></table></div>
          </div>
        </div>

        <!-- ALL ORDERS -->
        <div class="panel" id="sa-allorders">
          <div class="card">
            <div class="card-head"><div class="card-title">All Platform Orders</div></div>
            <div class="tbl-wrap"><table><thead><tr><th>Order ID</th><th>Customer</th><th>Restaurant</th><th>Items</th><th>Total</th><th>Commission</th><th>Time</th><th>Status</th><th>Actions</th></tr></thead><tbody id="sa-orders-table"></tbody></table></div>
          </div>
        </div>

        <!-- CUSTOMERS -->
        <div class="panel" id="sa-customers">
          <div class="card">
            <div class="card-head"><div class="card-title">Registered Customers</div></div>
            <div class="tbl-wrap"><table><thead><tr><th>Customer</th><th>Email</th><th>Orders</th><th>Total Spent</th><th>Joined</th></tr></thead><tbody id="sa-cust-table"></tbody></table></div>
          </div>
        </div>

        <!-- SETTINGS -->
        <div class="panel" id="sa-settings">
          <div class="card">
            <div class="card-head"><div class="card-title">Platform Settings</div></div>
            <div class="card-body">
              <div class="form-row" style="margin-bottom:16px">
                <div class="fg"><label>Platform Name</label><input class="fi" value="EATLAOS" id="setPlatName"></div>
                <div class="fg"><label>Default Commission (%)</label><input class="fi" type="number" value="15" id="setCommission" min="0" max="50"></div>
              </div>
              <div class="fg"><label>Platform Tagline</label><input class="fi" value="Multi-Restaurant Food Platform · Laos"></div>
              <div class="fg"><label>Support Email</label><input class="fi" value="support@eatlaos.la"></div>
              <div class="fg"><label>Delivery Fee ($)</label><input class="fi" type="number" value="2.00" step="0.5" style="max-width:180px"></div>
              <button class="btn btn-gold" onclick="saveSettings()">Save Settings</button>
            </div>
          </div>
        </div>

      </div>
    </main>
  </div>
  <!-- Mobile nav -->
  <nav class="mobile-bar">
    <div class="mobile-bar-inner">
      <button class="mb-btn active" onclick="saPanel('dashboard',this)"><span class="mb-icon">📊</span>Dashboard</button>
      <button class="mb-btn" onclick="saPanel('restaurants',this)"><span class="mb-icon">🏪</span>Restos</button>
      <button class="mb-btn" onclick="saPanel('allorders',this)"><span class="mb-icon">🧾</span>Orders</button>
      <button class="mb-btn" onclick="saPanel('revenue',this)"><span class="mb-icon">💰</span>Revenue</button>
    </div>
  </nav>
</div>

<!-- ══════════════════════════════
     OWNER SHELL
══════════════════════════════ -->
<div id="ownerPage" class="page">
  <div class="shell">
    <aside class="sidebar">
      <div class="sidebar-brand">
        <div class="sidebar-brand-name">EAT<span>LAOS</span></div>
        <div class="sidebar-role-badge badge-owner">🏪 Restaurant</div>
      </div>
      <nav class="sidebar-nav">
        <div class="nav-section">My Restaurant</div>
        <button class="nav-item active" onclick="owPanel('dashboard',this)"><span class="nav-icon">📊</span>Dashboard</button>
        <button class="nav-item" onclick="owPanel('orders',this)"><span class="nav-icon">🧾</span>Orders<span class="nav-badge" id="owNewBadge">0</span></button>
        <button class="nav-item" onclick="owPanel('menu',this)"><span class="nav-icon">🍽️</span>Menu Items</button>
        <button class="nav-item" onclick="owPanel('categories',this)"><span class="nav-icon">🏷️</span>Categories</button>
        <div class="nav-section">Account</div>
        <button class="nav-item" onclick="owPanel('profile',this)"><span class="nav-icon">🏪</span>Restaurant Profile</button>
      </nav>
      <div class="sidebar-footer">
        <div class="sidebar-user">
          <div class="sidebar-avatar">🏪</div>
          <div><div class="sidebar-username" id="ownerNameSidebar">Owner</div><div class="sidebar-userrole" id="ownerRestoSidebar">Restaurant</div></div>
          <button class="logout-btn" onclick="logout()">Out</button>
        </div>
      </div>
    </aside>
    <main class="main">
      <div class="topbar">
        <div class="topbar-title" id="owTitleBar">Dashboard</div>
        <div class="topbar-right">
          <button class="topbar-btn" id="owSoundBtn" onclick="toggleSound()">🔔 Sound ON</button>
        </div>
      </div>
      <div class="content-body">

        <!-- OWNER DASHBOARD -->
        <div class="panel active" id="ow-dashboard">
          <div class="stats-row cols4">
            <div class="stat-box"><div class="stat-icon">🧾</div><div class="stat-label">My Orders</div><div class="stat-value" id="ow-stat-orders">0</div></div>
            <div class="stat-box"><div class="stat-icon">💰</div><div class="stat-label">My Revenue</div><div class="stat-value" id="ow-stat-rev">$0</div><div class="stat-change" id="ow-comm-info">After commission</div></div>
            <div class="stat-box"><div class="stat-icon">🍽️</div><div class="stat-label">Menu Items</div><div class="stat-value" id="ow-stat-items">0</div></div>
            <div class="stat-box"><div class="stat-icon">⭐</div><div class="stat-label">Pending Orders</div><div class="stat-value" id="ow-stat-pending">0</div></div>
          </div>
          <div class="chart-box">
            <div class="chart-box-head"><div class="chart-box-title">My Sales This Week</div>
              <div class="chart-tabs"><button class="chart-tab active" onclick="owChart('revenue',this)">Revenue</button><button class="chart-tab" onclick="owChart('orders',this)">Orders</button><button class="chart-tab" onclick="owChart('items',this)">Top Items</button></div>
            </div>
            <div class="chart-wrap"><canvas id="owChart"></canvas></div>
          </div>
          <div class="card">
            <div class="card-head"><div class="card-title">Recent Orders</div><button class="btn btn-sm" onclick="owPanel('orders',null)">View All</button></div>
            <div class="tbl-wrap"><table><thead><tr><th>Order</th><th>Customer</th><th>Items</th><th>Total</th><th>My Earnings</th><th>Status</th><th>Action</th></tr></thead><tbody id="ow-recent-orders"></tbody></table></div>
          </div>
        </div>

        <!-- OWNER ORDERS -->
        <div class="panel" id="ow-orders">
          <div style="display:flex;gap:8px;margin-bottom:16px;flex-wrap:wrap;">
            <button class="btn active" id="of-all" onclick="owFilterOrders('all',this)" style="border-color:var(--gold);color:var(--gold)">All</button>
            <button class="btn" onclick="owFilterOrders('pending',this)">Pending</button>
            <button class="btn" onclick="owFilterOrders('preparing',this)">Preparing</button>
            <button class="btn" onclick="owFilterOrders('ready',this)">Ready</button>
            <button class="btn" onclick="owFilterOrders('delivered',this)">Delivered</button>
          </div>
          <div class="card">
            <div class="card-head"><div class="card-title">My Orders</div></div>
            <div class="tbl-wrap"><table><thead><tr><th>Order</th><th>Customer</th><th>Items</th><th>Total</th><th>My Earnings</th><th>Time</th><th>Status</th><th>Update</th><th>Receipt</th></tr></thead><tbody id="ow-orders-table"></tbody></table></div>
          </div>
        </div>

        <!-- OWNER MENU -->
        <div class="panel" id="ow-menu">
          <div class="card">
            <div class="card-head"><div class="card-title">Menu Items</div><button class="btn btn-gold" onclick="openOwnerItemModal()">+ Add Item</button></div>
            <div class="tbl-wrap"><table><thead><tr><th>Item</th><th>Category</th><th>Price</th><th>Status</th><th>Actions</th></tr></thead><tbody id="ow-menu-table"></tbody></table></div>
          </div>
        </div>

        <!-- OWNER CATEGORIES -->
        <div class="panel" id="ow-categories">
          <div class="card">
            <div class="card-head"><div class="card-title">Categories</div><button class="btn btn-gold" onclick="openModal('owCatModal')">+ Add Category</button></div>
            <table><thead><tr><th>Name</th><th>Icon</th><th>Items</th><th>Actions</th></tr></thead><tbody id="ow-cat-table"></tbody></table>
          </div>
        </div>

        <!-- OWNER PROFILE -->
        <div class="panel" id="ow-profile">
          <div class="card">
            <div class="card-head"><div class="card-title">Restaurant Profile</div></div>
            <div class="card-body" id="ow-profile-body"></div>
          </div>
        </div>

      </div>
    </main>
  </div>
  <nav class="mobile-bar">
    <div class="mobile-bar-inner">
      <button class="mb-btn active" onclick="owPanel('dashboard',this)"><span class="mb-icon">📊</span>Dashboard</button>
      <button class="mb-btn" onclick="owPanel('orders',this)"><span class="mb-icon">🧾</span>Orders</button>
      <button class="mb-btn" onclick="owPanel('menu',this)"><span class="mb-icon">🍽️</span>Menu</button>
      <button class="mb-btn" onclick="owPanel('profile',this)"><span class="mb-icon">🏪</span>Profile</button>
    </div>
  </nav>
</div>

<!-- ══════════════════════════════
     CUSTOMER SHELL
══════════════════════════════ -->
<div id="customerPage" class="page">
  <div class="shell">
    <aside class="sidebar">
      <div class="sidebar-brand">
        <div class="sidebar-brand-name">EAT<span>LAOS</span></div>
        <div class="sidebar-role-badge badge-customer">👤 Customer</div>
      </div>
      <nav class="sidebar-nav">
        <button class="nav-item active" onclick="custPanel('home',this)"><span class="nav-icon">🏠</span>Home</button>
        <button class="nav-item" onclick="custPanel('browse',this)"><span class="nav-icon">🍽️</span>Restaurants</button>
        <button class="nav-item" onclick="custPanel('myorders',this)"><span class="nav-icon">📋</span>My Orders</button>
      </nav>
      <div class="sidebar-footer">
        <div class="sidebar-user">
          <div class="sidebar-avatar">👤</div>
          <div><div class="sidebar-username" id="custNameSidebar">Customer</div><div class="sidebar-userrole">Customer Account</div></div>
          <button class="logout-btn" onclick="logout()">Out</button>
        </div>
      </div>
    </aside>
    <main class="main">
      <div class="topbar">
        <div class="topbar-title" id="custTitleBar">Welcome</div>
        <div class="topbar-right">
          <button class="topbar-btn" onclick="toggleCart()">🛒 Cart <span id="topCartCount" style="color:var(--gold);font-weight:700">0</span></button>
        </div>
      </div>
      <div class="content-body">

        <!-- HOME -->
        <div class="panel active" id="cust-home">
          <div style="padding:40px 0 32px">
            <div style="font-size:11px;letter-spacing:3px;text-transform:uppercase;color:var(--gold);margin-bottom:10px">Good day 👋</div>
            <div style="font-family:var(--font-head);font-size:48px;font-weight:800;line-height:1.1;margin-bottom:12px">What are you<br>hungry for?</div>
            <div style="font-size:14px;color:var(--gray);margin-bottom:28px">Order from the best restaurants in Vientiane</div>
            <button class="btn btn-gold" onclick="custPanel('browse',null)" style="padding:12px 28px;font-size:13px">Browse Restaurants →</button>
          </div>
          <div style="margin-bottom:12px;font-family:var(--font-head);font-size:18px;font-weight:700">Featured Restaurants</div>
          <div class="resto-grid" id="featuredRestos"></div>
        </div>

        <!-- BROWSE -->
        <div class="panel" id="cust-browse">
          <div class="resto-grid" id="allRestos" style="margin-bottom:0"></div>
        </div>

        <!-- RESTAURANT MENU -->
        <div class="panel" id="cust-restomenu">
          <div style="margin-bottom:20px;display:flex;align-items:center;gap:12px;flex-wrap:wrap;">
            <button class="btn" onclick="custPanel('browse',null)">← Back</button>
            <div>
              <div style="font-family:var(--font-head);font-size:24px;font-weight:800" id="rm-name"></div>
              <div style="font-size:11px;color:var(--gold);letter-spacing:2px;text-transform:uppercase" id="rm-cuisine"></div>
            </div>
          </div>
          <div style="display:flex;gap:0;overflow-x:auto;margin-bottom:20px;scrollbar-width:none;" id="rm-cats"></div>
          <div class="menu-grid-c" id="rm-grid"></div>
        </div>

        <!-- MY ORDERS -->
        <div class="panel" id="cust-myorders">
          <div id="cust-orders-list"></div>
        </div>

      </div>
    </main>
  </div>

  <!-- Cart FAB -->
  <button class="cart-fab" id="cartFab" onclick="toggleCart()">
    🛒 View Cart
    <span class="cart-fab-count" id="cartFabCount">0</span>
  </button>

  <!-- Cart Panel -->
  <div class="cart-overlay" id="cartOverlay" onclick="toggleCart()"></div>
  <div class="cart-panel" id="cartPanel">
    <div class="cart-ph"><div class="cart-ptitle">Your Cart</div><button class="modal-close" onclick="toggleCart()">✕</button></div>
    <div class="cart-items" id="cartItemsEl"></div>
    <div class="cart-foot" id="cartFootEl" style="display:none">
      <div class="cart-resto-name" id="cartRestoName"></div>
      <div class="cart-row"><span>Subtotal</span><span id="cartSub">$0</span></div>
      <div class="cart-row"><span>Delivery</span><span>$2.00</span></div>
      <div class="cart-total-row"><span class="cart-total-label">Total</span><span class="cart-total-val" id="cartTotalVal">$0</span></div>
      <button class="btn-order" onclick="placeOrder()">Place Order →</button>
    </div>
  </div>

  <nav class="mobile-bar">
    <div class="mobile-bar-inner">
      <button class="mb-btn active" onclick="custPanel('home',this)"><span class="mb-icon">🏠</span>Home</button>
      <button class="mb-btn" onclick="custPanel('browse',this)"><span class="mb-icon">🍽️</span>Browse</button>
      <button class="mb-btn" onclick="toggleCart()"><span class="mb-icon">🛒</span>Cart</button>
      <button class="mb-btn" onclick="custPanel('myorders',this)"><span class="mb-icon">📋</span>Orders</button>
    </div>
  </nav>
</div>

<!-- ══════════════════════════════
     MODALS
══════════════════════════════ -->

<!-- Add Restaurant -->
<div class="modal-bg" id="addRestoModal">
  <div class="modal-box wide">
    <div class="modal-head"><div class="modal-title" id="restoModalTitle">Add Restaurant</div><button class="modal-close" onclick="closeModal('addRestoModal')">✕</button></div>
    <div class="modal-body">
      <input type="hidden" id="editRestoId">
      <div class="form-row">
        <div class="fg"><label>Restaurant Name</label><input class="fi" id="rName" placeholder="e.g. Savaan Kitchen"></div>
        <div class="fg"><label>Cuisine Type</label><input class="fi" id="rCuisine" placeholder="e.g. Lao, Thai, Mixed"></div>
      </div>
      <div class="form-row">
        <div class="fg"><label>Owner Username</label><input class="fi" id="rOwnerUser" placeholder="login username"></div>
        <div class="fg"><label>Owner Password</label><input class="fi" type="password" id="rOwnerPass" placeholder="••••••••"></div>
      </div>
      <div class="form-row">
        <div class="fg"><label>Commission % (Platform takes)</label><input class="fi" type="number" id="rCommission" value="15" min="0" max="50"></div>
        <div class="fg"><label>Restaurant Emoji Icon</label><input class="fi" id="rEmoji" placeholder="🍽️" maxlength="4"></div>
      </div>
      <div class="fg"><label>Address</label><input class="fi" id="rAddress" placeholder="Vientiane, Laos"></div>
      <div class="form-row">
        <div class="fg"><label>Status</label><select class="fi" id="rStatus"><option value="active">Active</option><option value="pending">Pending Approval</option><option value="inactive">Inactive</option></select></div>
        <div class="fg"><label>Delivery Time (mins)</label><input class="fi" type="number" id="rDelivery" value="30"></div>
      </div>
    </div>
    <div class="modal-foot"><button class="btn" onclick="closeModal('addRestoModal')">Cancel</button><button class="btn btn-gold" onclick="saveResto()">Save Restaurant</button></div>
  </div>
</div>

<!-- Owner Add/Edit Item -->
<div class="modal-bg" id="owItemModal">
  <div class="modal-box">
    <div class="modal-head"><div class="modal-title" id="owItemModalTitle">Add Item</div><button class="modal-close" onclick="closeModal('owItemModal')">✕</button></div>
    <div class="modal-body">
      <input type="hidden" id="owEditItemId">
      <div class="fg"><label>Item Name</label><input class="fi" id="owItemName" placeholder="e.g. Grilled Salmon"></div>
      <div class="form-row">
        <div class="fg"><label>Category</label><select class="fi" id="owItemCat"></select></div>
        <div class="fg"><label>Price ($)</label><input class="fi" type="number" id="owItemPrice" placeholder="0.00" step="0.01"></div>
      </div>
      <div class="fg"><label>Description</label><input class="fi" id="owItemDesc" placeholder="Brief description..."></div>
      <div class="form-row">
        <div class="fg"><label>Emoji</label><input class="fi" id="owItemEmoji" placeholder="🍽️" maxlength="4"></div>
        <div class="fg"><label>Badge</label><select class="fi" id="owItemBadge"><option value="">None</option><option value="new">New</option><option value="popular">Popular</option></select></div>
      </div>
      <div class="fg"><label>Status</label><select class="fi" id="owItemStatus"><option value="active">Active</option><option value="inactive">Inactive</option></select></div>
    </div>
    <div class="modal-foot"><button class="btn" onclick="closeModal('owItemModal')">Cancel</button><button class="btn btn-gold" onclick="saveOwnerItem()">Save</button></div>
  </div>
</div>

<!-- Owner Add Category -->
<div class="modal-bg" id="owCatModal">
  <div class="modal-box" style="width:400px">
    <div class="modal-head"><div class="modal-title">Add Category</div><button class="modal-close" onclick="closeModal('owCatModal')">✕</button></div>
    <div class="modal-body">
      <div class="fg"><label>Category Name</label><input class="fi" id="owCatName" placeholder="e.g. Main Course"></div>
      <div class="fg"><label>Icon Emoji</label><input class="fi" id="owCatIcon" placeholder="🍽️" maxlength="4"></div>
    </div>
    <div class="modal-foot"><button class="btn" onclick="closeModal('owCatModal')">Cancel</button><button class="btn btn-gold" onclick="saveOwnerCat()">Save</button></div>
  </div>
</div>

<!-- Item Detail -->
<div class="modal-bg" id="itemDetailModal">
  <div class="modal-box detail-modal">
    <div class="detail-img" id="detail-emoji">🍽️</div>
    <div class="detail-body">
      <div style="font-size:10px;letter-spacing:2px;text-transform:uppercase;color:var(--gold);margin-bottom:6px" id="detail-cat"></div>
      <div class="detail-name" id="detail-name"></div>
      <div class="detail-desc" id="detail-desc"></div>
      <div style="display:flex;justify-content:space-between;align-items:flex-end;flex-wrap:wrap;gap:16px">
        <div>
          <div class="detail-price" id="detail-price"></div>
          <div class="detail-running" id="detail-running"></div>
        </div>
        <div class="qty-ctrl">
          <button class="qty-btn-lg" onclick="detailQtyChange(-1)">−</button>
          <span class="qty-num-lg" id="detail-qty">1</span>
          <button class="qty-btn-lg" onclick="detailQtyChange(1)">+</button>
        </div>
      </div>
      <div style="display:flex;gap:10px;margin-top:20px">
        <button class="btn btn-gold" style="flex:1;padding:13px" onclick="addDetailToCart()">Add to Cart →</button>
        <button class="btn" onclick="closeModal('itemDetailModal')">Close</button>
      </div>
    </div>
  </div>
</div>

<!-- Receipt -->
<div class="modal-bg" id="receiptModal">
  <div class="modal-box" style="width:380px">
    <div class="modal-head"><div class="modal-title">Receipt</div><button class="modal-close" onclick="closeModal('receiptModal')">✕</button></div>
    <div id="receiptContent" style="padding:8px"></div>
    <div class="modal-foot"><button class="btn" onclick="closeModal('receiptModal')">Close</button><button class="btn btn-gold" onclick="printReceipt()">🖨️ Print</button></div>
  </div>
</div>

<!-- Print area -->
<div id="printArea"></div>

<!-- Notifications -->
<div class="notif" id="notif"><span class="notif-icon" id="notifIcon">✓</span><span id="notifText"></span></div>
<div class="new-order-alert" id="newOrderAlert">🔔 New order received!</div>

<script>
// ═══════════════════════════════════════
// DATA STORE
// ═══════════════════════════════════════
const KEYS={restos:'el_restos',orders:'el_orders',customers:'el_customers',settings:'el_settings'};
let DB={
  settings:{commission:15,platformName:'EATLAOS',deliveryFee:2},
  restos: JSON.parse(localStorage.getItem(KEYS.restos)||'null') || [
    {id:'r1',name:'Savaan Kitchen',cuisine:'Lao & Thai',emoji:'🫕',address:'Ban Mixay, Vientiane',ownerUser:'savaan',ownerPass:'owner123',commission:15,status:'active',delivery:30,
     categories:[{id:'c1',name:'Lao Food',icon:'🫕'},{id:'c2',name:'Starters',icon:'🥗'},{id:'c3',name:'Drinks',icon:'🥤'}],
     menu:[
       {id:'m1',name:'Lao Larb',cat:'Lao Food',price:8.50,desc:'Traditional minced meat salad with fresh herbs',emoji:'🫕',badge:'popular',status:'active'},
       {id:'m2',name:'Tom Kha Soup',cat:'Starters',price:6.00,desc:'Creamy coconut milk soup with galangal',emoji:'🍲',badge:'',status:'active'},
       {id:'m3',name:'Sticky Rice',cat:'Lao Food',price:2.00,desc:'Traditional steamed glutinous rice',emoji:'🍚',badge:'',status:'active'},
       {id:'m4',name:'Lychee Smoothie',cat:'Drinks',price:4.00,desc:'Fresh lychee blended with coconut milk',emoji:'🥤',badge:'new',status:'active'},
     ]
    },
    {id:'r2',name:'Burger Lab',cuisine:'Western & Burgers',emoji:'🍔',address:'Nong Bone Road, Vientiane',ownerUser:'burgerlab',ownerPass:'owner123',commission:12,status:'active',delivery:25,
     categories:[{id:'c4',name:'Burgers',icon:'🍔'},{id:'c5',name:'Sides',icon:'🍟'},{id:'c6',name:'Drinks',icon:'🥤'}],
     menu:[
       {id:'m5',name:'Classic Cheeseburger',cat:'Burgers',price:12.00,desc:'Juicy beef patty with aged cheddar',emoji:'🍔',badge:'popular',status:'active'},
       {id:'m6',name:'Mushroom Burger',cat:'Burgers',price:11.00,desc:'Portobello with avocado and brie',emoji:'🍔',badge:'',status:'active'},
       {id:'m7',name:'Loaded Fries',cat:'Sides',price:5.50,desc:'Crispy fries with cheese and jalapenos',emoji:'🍟',badge:'popular',status:'active'},
       {id:'m8',name:'Milkshake',cat:'Drinks',price:6.00,desc:'Thick and creamy classic milkshake',emoji:'🥛',badge:'',status:'active'},
     ]
    },
    {id:'r3',name:'Pasta Palace',cuisine:'Italian',emoji:'🍝',address:'Setthathirat Road, Vientiane',ownerUser:'pastapalace',ownerPass:'owner123',commission:18,status:'active',delivery:35,
     categories:[{id:'c7',name:'Pasta',icon:'🍝'},{id:'c8',name:'Desserts',icon:'🍮'}],
     menu:[
       {id:'m9',name:'Truffle Pasta',cat:'Pasta',price:16.00,desc:'Fresh pasta with black truffle and parmesan',emoji:'🍝',badge:'new',status:'active'},
       {id:'m10',name:'Carbonara',cat:'Pasta',price:14.00,desc:'Classic Roman pasta with egg and pancetta',emoji:'🍝',badge:'popular',status:'active'},
       {id:'m11',name:'Tiramisu',cat:'Desserts',price:7.50,desc:'Classic Italian dessert with mascarpone',emoji:'🍮',badge:'',status:'active'},
     ]
    },
  ],
  orders: JSON.parse(localStorage.getItem(KEYS.orders)||'[]'),
  customers: JSON.parse(localStorage.getItem(KEYS.customers)||'[]'),
};

function saveDB(){
  localStorage.setItem(KEYS.restos,JSON.stringify(DB.restos));
  localStorage.setItem(KEYS.orders,JSON.stringify(DB.orders));
  localStorage.setItem(KEYS.customers,JSON.stringify(DB.customers));
}

// State
let currentUser=null, currentRole=null, currentRestoId=null;
let cart=[], cartRestoId=null;
let soundEnabled=true;
let saChartInst=null, saRestoChartInst=null, owChartInst=null;
let detailItemData=null, detailQtyVal=1;
let owEditItemId=null, activeMenuCat='All', lastOrderCount=DB.orders.length;
let selectedLoginRole='superadmin';

// ═══════════════════════════════════════
// LOGIN
// ═══════════════════════════════════════
function selectRole(role,el){
  selectedLoginRole=role;
  document.querySelectorAll('.role-card').forEach(c=>c.classList.remove('selected'));
  el.classList.add('selected');
  const cfg={
    superadmin:{title:'Super Admin Login',sub:'Full platform control panel',hint:'Use <b>superadmin</b> / <b>admin123</b>'},
    owner:{title:'Restaurant Owner Login',sub:'Manage your restaurant & menu',hint:'Try <b>savaan</b> / <b>owner123</b>'},
    customer:{title:'Customer Login',sub:'Browse & order from restaurants',hint:'Use <b>guest</b> or any email + password'},
  };
  const c=cfg[role];
  document.getElementById('loginTitle').textContent=c.title;
  document.getElementById('loginSub').textContent=c.sub;
  document.getElementById('loginHint').innerHTML=c.hint;
  document.getElementById('loginError').textContent='';
  document.getElementById('loginUser').value='';
  document.getElementById('loginPass').value='';
}

function doLogin(){
  const u=document.getElementById('loginUser').value.trim();
  const p=document.getElementById('loginPass').value;
  document.getElementById('loginError').textContent='';
  if(selectedLoginRole==='superadmin'){
    if(u==='superadmin'&&p==='admin123'){
      currentUser={name:'Super Admin',role:'superadmin'};
      showPage('superAdminPage');
      saPanel('dashboard',document.querySelector('#superAdminPage .nav-item'));
      setTimeout(()=>initSACharts(),200);
      startPolling();
    } else { document.getElementById('loginError').textContent='Wrong credentials'; }
  } else if(selectedLoginRole==='owner'){
    const resto=DB.restos.find(r=>r.ownerUser===u&&r.ownerPass===p);
    if(resto){
      currentUser={name:u,role:'owner',restoId:resto.id};
      currentRestoId=resto.id;
      document.getElementById('ownerNameSidebar').textContent=u;
      document.getElementById('ownerRestoSidebar').textContent=resto.name;
      showPage('ownerPage');
      owPanel('dashboard',document.querySelector('#ownerPage .nav-item'));
      setTimeout(()=>initOwChart('revenue'),200);
      startPolling();
    } else { document.getElementById('loginError').textContent='Wrong credentials'; }
  } else {
    if(!u){document.getElementById('loginError').textContent='Enter username/email';return;}
    let cust=DB.customers.find(c=>c.email===u||c.name===u);
    if(!cust){
      cust={id:'cust_'+Date.now(),name:u,email:u,joined:new Date().toLocaleDateString(),orders:0,spent:0};
      DB.customers.push(cust); saveDB();
    }
    currentUser={name:cust.name,role:'customer',custId:cust.id};
    document.getElementById('custNameSidebar').textContent=cust.name;
    showPage('customerPage');
    custPanel('home',document.querySelector('#customerPage .nav-item'));
    renderFeaturedRestos(); renderAllRestos();
  }
}

function logout(){currentUser=null;currentRole=null;currentRestoId=null;cart=[];cartRestoId=null;showPage('staffLoginPage');}
function showPage(id){document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));document.getElementById(id).classList.add('active');}

// ═══════════════════════════════════════
// SUPER ADMIN PANELS
// ═══════════════════════════════════════
function saPanel(panel,btn){
  document.querySelectorAll('#superAdminPage .panel').forEach(p=>p.classList.remove('active'));
  document.getElementById('sa-'+panel).classList.add('active');
  document.querySelectorAll('#superAdminPage .nav-item').forEach(n=>n.classList.remove('active'));
  if(btn) btn.classList.add('active');
  const t={dashboard:'Dashboard',revenue:'Revenue & Commission',restaurants:'Restaurants',allorders:'All Orders',customers:'Customers',settings:'Platform Settings'};
  document.getElementById('saTitleBar').textContent=t[panel]||panel;
  if(panel==='dashboard'){updateSAStats();renderSARecentOrders();setTimeout(()=>initSACharts(),100);}
  if(panel==='revenue') renderSARevenue();
  if(panel==='restaurants') renderSARestos();
  if(panel==='allorders') renderSAAllOrders();
  if(panel==='customers') renderSACustomers();
}
function updateSAStats(){
  document.getElementById('sa-stat-restos').textContent=DB.restos.filter(r=>r.status==='active').length;
  document.getElementById('sa-stat-orders').textContent=DB.orders.length;
  const comm=DB.orders.reduce((s,o)=>s+(parseFloat(o.total||0)*((o.commission||15)/100)),0);
  document.getElementById('sa-stat-rev').textContent='$'+comm.toFixed(0);
  document.getElementById('sa-stat-cust').textContent=DB.customers.length;
  document.getElementById('pendingBadge').textContent=DB.restos.filter(r=>r.status==='pending').length;
}
function renderSARecentOrders(){
  const tb=document.getElementById('sa-recent-orders');
  tb.innerHTML=DB.orders.slice(0,8).map(o=>{
    const comm=(parseFloat(o.total||0)*(o.commission||15)/100).toFixed(2);
    return`<tr><td><div class="td-name">${o.id}</div></td><td>${o.customer}</td><td><span style="color:var(--gold)">${o.restoName||'—'}</span></td><td style="color:var(--gold);font-weight:600">$${o.total}</td><td style="color:var(--green)">$${comm}</td><td><span class="tag tag-${statusColor(o.status)}">${o.status}</span></td></tr>`;
  }).join('')||'<tr><td colspan="6" style="text-align:center;color:var(--gray);padding:30px">No orders yet</td></tr>';
}
function renderSARevenue(){
  const gross=DB.orders.reduce((s,o)=>s+parseFloat(o.total||0),0);
  const commPct=DB.settings.commission;
  const comm=DB.orders.reduce((s,o)=>s+(parseFloat(o.total||0)*((o.commission||commPct)/100)),0);
  document.getElementById('sa-gross').textContent='$'+gross.toFixed(2);
  document.getElementById('sa-commission').textContent='$'+comm.toFixed(2);
  document.getElementById('sa-comm-pct').textContent='At '+commPct+'% default';
  document.getElementById('sa-paid-out').textContent='$'+(gross-comm).toFixed(2);
  const tb=document.getElementById('sa-revenue-table');
  tb.innerHTML=DB.restos.map(r=>{
    const rOrders=DB.orders.filter(o=>o.restoId===r.id);
    const gross2=rOrders.reduce((s,o)=>s+parseFloat(o.total||0),0);
    const myComm=gross2*(r.commission/100);
    const restoEarn=gross2-myComm;
    return`<tr><td><div class="td-name">${r.emoji} ${r.name}</div></td><td>${rOrders.length}</td><td>$${gross2.toFixed(2)}</td><td><span class="commission-badge">${r.commission}%</span></td><td style="color:var(--gold);font-weight:600">$${myComm.toFixed(2)}</td><td style="color:var(--green)">$${restoEarn.toFixed(2)}</td></tr>`;
  }).join('');
}
function renderSARestos(){
  const tb=document.getElementById('sa-restos-table');
  tb.innerHTML=DB.restos.map(r=>{
    const cnt=DB.orders.filter(o=>o.restoId===r.id).length;
    return`<tr>
      <td><div class="td-name">${r.emoji} ${r.name}</div><div class="td-sub">${r.address}</div></td>
      <td>${r.ownerUser}</td>
      <td style="color:var(--gold)">${r.cuisine}</td>
      <td><span class="commission-badge">${r.commission}%</span></td>
      <td><span class="tag tag-${r.status==='active'?'green':r.status==='pending'?'orange':'red'}">${r.status}</span></td>
      <td>${cnt}</td>
      <td><div class="actions">
        <button class="btn btn-sm" onclick="editResto('${r.id}')">Edit</button>
        <button class="btn btn-sm btn-red" onclick="deleteResto('${r.id}')">Del</button>
        ${r.status==='pending'?`<button class="btn btn-sm btn-gold" onclick="approveResto('${r.id}')">Approve</button>`:''}
      </div></td>
    </tr>`;
  }).join('');
}
function renderSAAllOrders(){
  const tb=document.getElementById('sa-orders-table');
  tb.innerHTML=DB.orders.map(o=>{
    const comm=(parseFloat(o.total||0)*(o.commission||15)/100).toFixed(2);
    return`<tr>
      <td><strong>${o.id}</strong></td><td>${o.customer}</td>
      <td style="color:var(--gold)">${o.restoName||'—'}</td>
      <td>${o.items?.length||0} items</td>
      <td style="color:var(--gold);font-weight:600">$${o.total}</td>
      <td style="color:var(--green)">$${comm}</td>
      <td style="color:var(--gray);font-size:11px">${o.time}</td>
      <td><span class="tag tag-${statusColor(o.status)}">${o.status}</span></td>
      <td><button class="btn btn-sm btn-icon" onclick="openReceipt('${o.id}')">🖨️</button></td>
    </tr>`;
  }).join('')||'<tr><td colspan="9" style="text-align:center;color:var(--gray);padding:30px">No orders yet</td></tr>';
}
function renderSACustomers(){
  const tb=document.getElementById('sa-cust-table');
  tb.innerHTML=DB.customers.map(c=>{
    const spent=DB.orders.filter(o=>o.customer===c.name).reduce((s,o)=>s+parseFloat(o.total||0),0);
    const cnt=DB.orders.filter(o=>o.customer===c.name).length;
    return`<tr><td><div class="td-name">👤 ${c.name}</div></td><td style="color:var(--gray)">${c.email}</td><td>${cnt}</td><td style="color:var(--gold)">$${spent.toFixed(2)}</td><td style="color:var(--gray);font-size:11px">${c.joined}</td></tr>`;
  }).join('')||'<tr><td colspan="5" style="text-align:center;color:var(--gray);padding:30px">No customers yet</td></tr>';
}

// SA Charts
function initSACharts(){
  initSARevenueChart('week'); initSARestoChart();
}
function initSARevenueChart(period){
  const ctx=document.getElementById('saRevenueChart');
  if(!ctx) return;
  if(saChartInst) saChartInst.destroy();
  const labels=period==='week'?['Mon','Tue','Wed','Thu','Fri','Sat','Sun']:['Jan','Feb','Mar','Apr','May','Jun'];
  const base=period==='week'?[45,68,52,89,120,145,98]:[320,410,380,520,640,580];
  saChartInst=new Chart(ctx,{type:'bar',data:{labels,datasets:[{label:'Commission ($)',data:base,backgroundColor:'rgba(232,184,75,.7)',borderColor:'var(--gold)',borderWidth:0,borderRadius:3}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{grid:{display:false},ticks:{color:'#666',font:{size:10}}},y:{grid:{color:'#222'},ticks:{color:'#666',font:{size:10},callback:v=>'$'+v}}}}});
}
function initSARestoChart(){
  const ctx=document.getElementById('saRestoChart');
  if(!ctx) return;
  if(saRestoChartInst) saRestoChartInst.destroy();
  const labels=DB.restos.map(r=>r.name);
  const data=DB.restos.map(r=>DB.orders.filter(o=>o.restoId===r.id).length||Math.floor(Math.random()*30+5));
  saRestoChartInst=new Chart(ctx,{type:'doughnut',data:{labels,datasets:[{data,backgroundColor:['rgba(232,184,75,.8)','rgba(46,204,113,.8)','rgba(52,152,219,.8)','rgba(231,76,60,.8)'],borderColor:'#161616',borderWidth:2}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{position:'right',labels:{color:'#888',font:{size:10},boxWidth:10}}}}});
}
function saChart(period,btn){document.querySelectorAll('.chart-tab').forEach(t=>t.classList.remove('active'));btn.classList.add('active');initSARevenueChart(period);}

// Resto CRUD
function saveResto(){
  const id=document.getElementById('editRestoId').value;
  const data={
    name:document.getElementById('rName').value.trim(),
    cuisine:document.getElementById('rCuisine').value.trim(),
    ownerUser:document.getElementById('rOwnerUser').value.trim(),
    ownerPass:document.getElementById('rOwnerPass').value||'owner123',
    commission:parseInt(document.getElementById('rCommission').value)||15,
    emoji:document.getElementById('rEmoji').value.trim()||'🍽️',
    address:document.getElementById('rAddress').value.trim(),
    status:document.getElementById('rStatus').value,
    delivery:parseInt(document.getElementById('rDelivery').value)||30,
  };
  if(!data.name||!data.ownerUser){notify('⚠️','Fill required fields');return;}
  if(id){
    const r=DB.restos.find(x=>x.id===id);
    if(r) Object.assign(r,data);
    notify('✓','Restaurant updated');
  } else {
    DB.restos.push({...data,id:'r'+Date.now(),categories:[{id:'c'+Date.now(),name:'Main Course',icon:'🍽️'}],menu:[]});
    notify('✓','Restaurant added');
  }
  saveDB(); closeModal('addRestoModal'); renderSARestos(); updateSAStats();
}
function editResto(id){
  const r=DB.restos.find(x=>x.id===id);
  if(!r) return;
  document.getElementById('restoModalTitle').textContent='Edit Restaurant';
  document.getElementById('editRestoId').value=id;
  document.getElementById('rName').value=r.name;
  document.getElementById('rCuisine').value=r.cuisine;
  document.getElementById('rOwnerUser').value=r.ownerUser;
  document.getElementById('rOwnerPass').value='';
  document.getElementById('rCommission').value=r.commission;
  document.getElementById('rEmoji').value=r.emoji;
  document.getElementById('rAddress').value=r.address;
  document.getElementById('rStatus').value=r.status;
  document.getElementById('rDelivery').value=r.delivery;
  openModal('addRestoModal');
}
function deleteResto(id){DB.restos=DB.restos.filter(r=>r.id!==id);saveDB();renderSARestos();updateSAStats();notify('🗑️','Restaurant removed');}
function approveResto(id){const r=DB.restos.find(x=>x.id===id);if(r){r.status='active';saveDB();renderSARestos();updateSAStats();notify('✓','Restaurant approved!');}}
function saveSettings(){DB.settings.commission=parseInt(document.getElementById('setCommission').value)||15;notify('✓','Settings saved');}

// ═══════════════════════════════════════
// OWNER PANELS
// ═══════════════════════════════════════
function getResto(){return DB.restos.find(r=>r.id===currentRestoId);}
function getOwnerOrders(filter='all'){
  const all=DB.orders.filter(o=>o.restoId===currentRestoId);
  return filter==='all'?all:all.filter(o=>o.status===filter);
}

function owPanel(panel,btn){
  document.querySelectorAll('#ownerPage .panel').forEach(p=>p.classList.remove('active'));
  document.getElementById('ow-'+panel).classList.add('active');
  document.querySelectorAll('#ownerPage .nav-item').forEach(n=>n.classList.remove('active'));
  if(btn) btn.classList.add('active');
  const t={dashboard:'Dashboard',orders:'Orders',menu:'Menu Items',categories:'Categories',profile:'Restaurant Profile'};
  document.getElementById('owTitleBar').textContent=t[panel]||panel;
  if(panel==='dashboard'){updateOwStats();renderOwRecentOrders();setTimeout(()=>initOwChart('revenue'),100);}
  if(panel==='orders') renderOwOrders();
  if(panel==='menu') renderOwMenu();
  if(panel==='categories') renderOwCats();
  if(panel==='profile') renderOwProfile();
}
function updateOwStats(){
  const r=getResto(); if(!r) return;
  const myOrders=DB.orders.filter(o=>o.restoId===r.id);
  const gross=myOrders.reduce((s,o)=>s+parseFloat(o.total||0),0);
  const myEarn=gross*(1-r.commission/100);
  const pending=myOrders.filter(o=>o.status==='pending').length;
  document.getElementById('ow-stat-orders').textContent=myOrders.length;
  document.getElementById('ow-stat-rev').textContent='$'+myEarn.toFixed(0);
  document.getElementById('ow-stat-items').textContent=r.menu.filter(m=>m.status==='active').length;
  document.getElementById('ow-stat-pending').textContent=pending;
  document.getElementById('ow-comm-info').textContent=`After ${r.commission}% commission`;
  document.getElementById('owNewBadge').textContent=pending;
}
function renderOwRecentOrders(){
  const r=getResto(); if(!r) return;
  const tb=document.getElementById('ow-recent-orders');
  const orders=getOwnerOrders().slice(0,6);
  tb.innerHTML=orders.map(o=>{
    const earn=(parseFloat(o.total||0)*(1-r.commission/100)).toFixed(2);
    return`<tr><td><strong>${o.id}</strong></td><td>${o.customer}</td><td>${o.items?.length||0} items</td><td style="color:var(--gold)">$${o.total}</td><td style="color:var(--green)">$${earn}</td><td><span class="tag tag-${statusColor(o.status)}">${o.status}</span></td><td>${statusSelect(o.id,o.status)}</td></tr>`;
  }).join('')||'<tr><td colspan="7" style="text-align:center;color:var(--gray);padding:30px">No orders yet</td></tr>';
}
function renderOwOrders(filter='all'){
  const r=getResto(); if(!r) return;
  const tb=document.getElementById('ow-orders-table');
  const orders=getOwnerOrders(filter);
  tb.innerHTML=orders.map(o=>{
    const earn=(parseFloat(o.total||0)*(1-r.commission/100)).toFixed(2);
    return`<tr><td><strong>${o.id}</strong></td><td>${o.customer}</td><td style="max-width:120px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${o.items?.map(i=>i.emoji+i.name).join(', ')||'—'}</td><td style="color:var(--gold)">$${o.total}</td><td style="color:var(--green)">$${earn}</td><td style="color:var(--gray);font-size:11px">${o.time}</td><td><span class="tag tag-${statusColor(o.status)}">${o.status}</span></td><td>${statusSelect(o.id,o.status)}</td><td><button class="btn btn-sm btn-icon" onclick="openReceipt('${o.id}')">🖨️</button></td></tr>`;
  }).join('')||'<tr><td colspan="9" style="text-align:center;color:var(--gray);padding:30px">No orders</td></tr>';
}
function owFilterOrders(f,btn){document.querySelectorAll('#ownerPage .btn').forEach(b=>b.style.borderColor='');document.querySelectorAll('#ownerPage .btn').forEach(b=>b.style.color='');if(btn){btn.style.borderColor='var(--gold)';btn.style.color='var(--gold)';}renderOwOrders(f);}
function renderOwMenu(){
  const r=getResto(); if(!r) return;
  document.getElementById('ow-menu-table').innerHTML=r.menu.map(item=>`
    <tr><td><div style="display:flex;align-items:center;gap:10px"><div style="width:40px;height:40px;background:var(--surface2);display:flex;align-items:center;justify-content:center;font-size:20px">${item.emoji}</div><div><div class="td-name">${item.name}</div><div class="td-sub">${item.desc.substring(0,40)}...</div></div></div></td>
    <td style="color:var(--gold)">${item.cat}</td><td style="font-weight:600">$${item.price.toFixed(2)}</td>
    <td><span class="tag tag-${item.status==='active'?'green':'gray'}">${item.status}</span></td>
    <td><div class="actions"><button class="btn btn-sm" onclick="editOwnerItem('${item.id}')">Edit</button><button class="btn btn-sm btn-red" onclick="deleteOwnerItem('${item.id}')">Del</button></div></td></tr>
  `).join('');
}
function renderOwCats(){
  const r=getResto(); if(!r) return;
  document.getElementById('ow-cat-table').innerHTML=r.categories.map(c=>`
    <tr><td><strong>${c.name}</strong></td><td style="font-size:22px">${c.icon}</td><td>${r.menu.filter(m=>m.cat===c.name).length}</td>
    <td><button class="btn btn-sm btn-red" onclick="deleteOwnerCat('${c.id}')">Delete</button></td></tr>
  `).join('');
}
function renderOwProfile(){
  const r=getResto(); if(!r) return;
  document.getElementById('ow-profile-body').innerHTML=`
    <div class="form-row" style="margin-bottom:16px">
      <div class="fg"><label>Restaurant Name</label><input class="fi" value="${r.name}" id="owpName"></div>
      <div class="fg"><label>Cuisine</label><input class="fi" value="${r.cuisine}" id="owpCuisine"></div>
    </div>
    <div class="form-row" style="margin-bottom:16px">
      <div class="fg"><label>Emoji Icon</label><input class="fi" value="${r.emoji}" id="owpEmoji" maxlength="4"></div>
      <div class="fg"><label>Delivery Time (mins)</label><input class="fi" type="number" value="${r.delivery}" id="owpDelivery"></div>
    </div>
    <div class="fg"><label>Address</label><input class="fi" value="${r.address}" id="owpAddress"></div>
    <div style="padding:16px;background:var(--surface2);border:1px solid var(--border);margin-bottom:16px">
      <div style="font-size:11px;color:var(--gray);margin-bottom:4px">PLATFORM COMMISSION</div>
      <div style="font-family:var(--font-head);font-size:24px;color:var(--gold)">${r.commission}%</div>
      <div style="font-size:12px;color:var(--gray);margin-top:4px">Set by platform admin · You earn ${100-r.commission}% of each order</div>
    </div>
    <button class="btn btn-gold" onclick="saveOwProfile()">Save Profile</button>
  `;
}
function saveOwProfile(){
  const r=getResto(); if(!r) return;
  r.name=document.getElementById('owpName').value.trim()||r.name;
  r.cuisine=document.getElementById('owpCuisine').value.trim()||r.cuisine;
  r.emoji=document.getElementById('owpEmoji').value.trim()||r.emoji;
  r.delivery=parseInt(document.getElementById('owpDelivery').value)||r.delivery;
  r.address=document.getElementById('owpAddress').value.trim()||r.address;
  saveDB(); notify('✓','Profile saved');
}

// Owner item CRUD
function openOwnerItemModal(){
  owEditItemId=null;
  document.getElementById('owItemModalTitle').textContent='Add Menu Item';
  ['owEditItemId','owItemName','owItemDesc','owItemPrice'].forEach(id=>document.getElementById(id).value='');
  document.getElementById('owItemEmoji').value='🍽️';
  document.getElementById('owItemBadge').value='';
  document.getElementById('owItemStatus').value='active';
  populateOwCatSelect(); openModal('owItemModal');
}
function editOwnerItem(id){
  const r=getResto(); if(!r) return;
  const item=r.menu.find(m=>m.id===id); if(!item) return;
  owEditItemId=id;
  document.getElementById('owItemModalTitle').textContent='Edit Item';
  document.getElementById('owItemName').value=item.name;
  document.getElementById('owItemDesc').value=item.desc;
  document.getElementById('owItemPrice').value=item.price;
  document.getElementById('owItemEmoji').value=item.emoji;
  document.getElementById('owItemBadge').value=item.badge||'';
  document.getElementById('owItemStatus').value=item.status;
  populateOwCatSelect(item.cat); openModal('owItemModal');
}
function populateOwCatSelect(sel=''){
  const r=getResto(); if(!r) return;
  document.getElementById('owItemCat').innerHTML=r.categories.map(c=>`<option value="${c.name}" ${c.name===sel?'selected':''}>${c.icon} ${c.name}</option>`).join('');
}
function saveOwnerItem(){
  const r=getResto(); if(!r) return;
  const name=document.getElementById('owItemName').value.trim();
  const price=parseFloat(document.getElementById('owItemPrice').value);
  const cat=document.getElementById('owItemCat').value;
  if(!name||!price||!cat){notify('⚠️','Fill required fields');return;}
  const data={name,price,cat,desc:document.getElementById('owItemDesc').value.trim(),emoji:document.getElementById('owItemEmoji').value.trim()||'🍽️',badge:document.getElementById('owItemBadge').value,status:document.getElementById('owItemStatus').value};
  if(owEditItemId){const item=r.menu.find(m=>m.id===owEditItemId);if(item) Object.assign(item,data);notify('✓','Item updated');}
  else{r.menu.push({...data,id:'m'+Date.now()});notify('✓','Item added');}
  saveDB(); closeModal('owItemModal'); renderOwMenu(); updateOwStats();
}
function deleteOwnerItem(id){const r=getResto();if(!r) return;r.menu=r.menu.filter(m=>m.id!==id);saveDB();renderOwMenu();updateOwStats();notify('🗑️','Item deleted');}
function saveOwnerCat(){
  const r=getResto(); if(!r) return;
  const name=document.getElementById('owCatName').value.trim();
  const icon=document.getElementById('owCatIcon').value.trim()||'🏷️';
  if(!name){notify('⚠️','Enter category name');return;}
  r.categories.push({id:'c'+Date.now(),name,icon});
  saveDB(); closeModal('owCatModal'); renderOwCats(); notify('✓','Category added');
}
function deleteOwnerCat(id){const r=getResto();if(!r) return;r.categories=r.categories.filter(c=>c.id!==id);saveDB();renderOwCats();notify('🗑️','Category deleted');}

// Owner chart
function initOwChart(type){
  const ctx=document.getElementById('owChart'); if(!ctx) return;
  if(owChartInst) owChartInst.destroy();
  const r=getResto(); if(!r) return;
  const days=['Mon','Tue','Wed','Thu','Fri','Sat','Sun'];
  let data,label;
  if(type==='revenue'){data=[60,80,55,95,130,160,110].map(v=>v*(1-r.commission/100));label='My Earnings ($)';}
  else if(type==='orders'){data=[6,9,7,11,16,20,14];label='Orders';}
  else{
    const counts={};
    DB.orders.filter(o=>o.restoId===r.id).forEach(o=>o.items?.forEach(i=>{counts[i.name]=(counts[i.name]||0)+i.qty;}));
    const sorted=Object.entries(counts).sort((a,b)=>b[1]-a[1]).slice(0,6);
    owChartInst=new Chart(ctx,{type:'doughnut',data:{labels:sorted.map(s=>s[0])||r.menu.slice(0,4).map(m=>m.name),datasets:[{data:sorted.map(s=>s[1])||r.menu.slice(0,4).map(()=>Math.floor(Math.random()*20+3)),backgroundColor:['rgba(232,184,75,.8)','rgba(46,204,113,.8)','rgba(52,152,219,.8)','rgba(231,76,60,.8)'],borderColor:'#161616',borderWidth:2}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{position:'right',labels:{color:'#888',font:{size:10},boxWidth:10}}}}});
    return;
  }
  owChartInst=new Chart(ctx,{type:'bar',data:{labels:days,datasets:[{label,data,backgroundColor:'rgba(232,184,75,.7)',borderWidth:0,borderRadius:3}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{grid:{display:false},ticks:{color:'#666',font:{size:10}}},y:{grid:{color:'#222'},ticks:{color:'#666',font:{size:10}}}}}});
}
function owChart(type,btn){document.querySelectorAll('#ownerPage .chart-tab').forEach(t=>t.classList.remove('active'));btn.classList.add('active');initOwChart(type);}

// ═══════════════════════════════════════
// CUSTOMER
// ═══════════════════════════════════════
function custPanel(panel,btn){
  document.querySelectorAll('#customerPage .panel').forEach(p=>p.classList.remove('active'));
  document.getElementById('cust-'+panel).classList.add('active');
  document.querySelectorAll('#customerPage .nav-item').forEach(n=>n.classList.remove('active'));
  if(btn) btn.classList.add('active');
  const t={home:'Welcome',browse:'Restaurants',restomenu:'Menu',myorders:'My Orders'};
  document.getElementById('custTitleBar').textContent=t[panel]||panel;
  if(panel==='myorders') renderCustOrders();
}
function restoCard(r){
  return`<div class="resto-card" onclick="openResto('${r.id}')">
    <div class="resto-status"><span class="tag tag-${r.status==='active'?'green':'red'}">${r.status}</span></div>
    <div class="resto-img-wrap"><div class="resto-img">${r.emoji}</div></div>
    <div class="resto-body">
      <div class="resto-name">${r.name}</div>
      <div class="resto-cuisine">${r.cuisine}</div>
      <div class="resto-meta"><span>🕐 ${r.delivery} min</span><span>📍 ${r.address.split(',')[0]}</span></div>
    </div>
  </div>`;
}
function renderFeaturedRestos(){document.getElementById('featuredRestos').innerHTML=DB.restos.filter(r=>r.status==='active').map(restoCard).join('');}
function renderAllRestos(){document.getElementById('allRestos').innerHTML=DB.restos.filter(r=>r.status==='active').map(restoCard).join('');}
function openResto(id){
  const r=DB.restos.find(x=>x.id===id); if(!r) return;
  currentRestoId=id;
  document.getElementById('rm-name').textContent=r.name;
  document.getElementById('rm-cuisine').textContent=r.cuisine;
  renderRestoMenu('All');
  custPanel('restomenu',null);
}
function renderRestoMenu(cat){
  const r=DB.restos.find(x=>x.id===currentRestoId); if(!r) return;
  activeMenuCat=cat;
  const cats=[{id:'all',name:'All',icon:'✦'},...r.categories];
  document.getElementById('rm-cats').innerHTML=cats.map(c=>`<button class="btn ${activeMenuCat===c.name?'btn-gold':''}" style="border-right:none;flex-shrink:0;white-space:nowrap" onclick="renderRestoMenu('${c.name}')">${c.icon||''} ${c.name}</button>`).join('');
  const items=r.menu.filter(m=>m.status==='active'&&(cat==='All'||m.cat===cat));
  document.getElementById('rm-grid').innerHTML=items.map(item=>`
    <div class="menu-card-c" onclick="openItemDetail('${item.id}','${r.id}')">
      ${item.badge==='new'?'<div style="position:absolute;top:10px;left:10px;background:var(--gold);color:#000;font-size:9px;letter-spacing:2px;padding:3px 8px;font-weight:700;text-transform:uppercase">New</div>':''}
      ${item.badge==='popular'?'<div style="position:absolute;top:10px;left:10px;background:var(--surface);border:1px solid var(--border);color:var(--gold);font-size:9px;letter-spacing:1px;padding:3px 8px;text-transform:uppercase">★ Popular</div>':''}
      <div class="mci">${item.emoji}</div>
      <div class="mcb">
        <div class="mc-cat">${item.cat}</div>
        <div class="mc-name">${item.name}</div>
        <div class="mc-desc">${item.desc}</div>
        <div class="mc-foot">
          <div class="mc-price">$${item.price.toFixed(2)}</div>
          <button class="mc-add" onclick="event.stopPropagation();quickAddToCart('${item.id}','${r.id}')">+</button>
        </div>
      </div>
    </div>
  `).join('')||'<div style="grid-column:1/-1;text-align:center;padding:60px;color:var(--gray)">No items in this category</div>';
}

// Item detail
function openItemDetail(itemId,restoId){
  const r=DB.restos.find(x=>x.id===restoId); if(!r) return;
  const item=r.menu.find(m=>m.id===itemId); if(!item) return;
  detailItemData={...item,restoId};
  detailQtyVal=1;
  document.getElementById('detail-emoji').textContent=item.emoji;
  document.getElementById('detail-cat').textContent=item.cat;
  document.getElementById('detail-name').textContent=item.name;
  document.getElementById('detail-desc').textContent=item.desc;
  document.getElementById('detail-price').textContent='$'+item.price.toFixed(2);
  document.getElementById('detail-qty').textContent=1;
  updateDetailRunning();
  openModal('itemDetailModal');
}
function detailQtyChange(d){detailQtyVal=Math.max(1,detailQtyVal+d);document.getElementById('detail-qty').textContent=detailQtyVal;updateDetailRunning();}
function updateDetailRunning(){if(!detailItemData) return;document.getElementById('detail-running').textContent=`${detailQtyVal} × $${detailItemData.price.toFixed(2)} = $${(detailItemData.price*detailQtyVal).toFixed(2)}`;}
function addDetailToCart(){
  if(!detailItemData) return;
  addCartItem(detailItemData,detailItemData.restoId,detailQtyVal);
  closeModal('itemDetailModal');
}
function quickAddToCart(itemId,restoId){
  const r=DB.restos.find(x=>x.id===restoId); if(!r) return;
  const item=r.menu.find(m=>m.id===itemId); if(!item) return;
  addCartItem({...item,restoId},restoId,1);
}
function addCartItem(item,restoId,qty){
  if(cartRestoId&&cartRestoId!==restoId){if(!confirm('Your cart has items from another restaurant. Clear and add new item?')) return; cart=[]; }
  cartRestoId=restoId;
  const ex=cart.find(c=>c.id===item.id);
  if(ex) ex.qty+=qty; else cart.push({...item,qty});
  updateCartUI();
  const r=DB.restos.find(x=>x.id===restoId);
  notify('✓',`${item.emoji} ${item.name} ×${qty} added`);
}

// Cart
function updateCartUI(){
  const count=cart.reduce((s,i)=>s+i.qty,0);
  document.getElementById('topCartCount').textContent=count;
  document.getElementById('cartFabCount').textContent=count;
  const fab=document.getElementById('cartFab');
  count>0?fab.classList.add('visible'):fab.classList.remove('visible');
  const el=document.getElementById('cartItemsEl');
  const foot=document.getElementById('cartFootEl');
  if(cart.length===0){
    el.innerHTML=`<div class="cart-empty-state"><div style="font-size:48px;margin-bottom:14px">🛒</div><div style="font-family:var(--font-head);font-size:20px;font-weight:700;margin-bottom:6px">Cart is empty</div><div style="font-size:13px">Add items to get started</div></div>`;
    foot.style.display='none'; return;
  }
  const r=DB.restos.find(x=>x.id===cartRestoId);
  document.getElementById('cartRestoName').textContent='Ordering from: '+(r?.name||'');
  el.innerHTML=cart.map(item=>`
    <div class="ci">
      <div class="ci-emoji">${item.emoji}</div>
      <div class="ci-info">
        <div class="ci-name">${item.name}</div>
        <div class="ci-price">$${(item.price*item.qty).toFixed(2)}</div>
        <div class="ci-qty">
          <button class="ci-qbtn" onclick="cartQty('${item.id}',-1)">−</button>
          <span class="ci-qnum">${item.qty}</span>
          <button class="ci-qbtn" onclick="cartQty('${item.id}',1)">+</button>
        </div>
      </div>
      <button class="ci-remove" onclick="cartRemove('${item.id}')">✕</button>
    </div>
  `).join('');
  const sub=cart.reduce((s,i)=>s+(i.price*i.qty),0);
  document.getElementById('cartSub').textContent='$'+sub.toFixed(2);
  document.getElementById('cartTotalVal').textContent='$'+(sub+2).toFixed(2);
  foot.style.display='block';
}
function cartQty(id,d){const item=cart.find(c=>c.id===id);if(!item) return;item.qty+=d;if(item.qty<=0) cartRemove(id);else updateCartUI();}
function cartRemove(id){cart=cart.filter(c=>c.id!==id);if(cart.length===0) cartRestoId=null;updateCartUI();}
function toggleCart(){document.getElementById('cartOverlay').classList.toggle('open');document.getElementById('cartPanel').classList.toggle('open');updateCartUI();}
function placeOrder(){
  if(!cart.length) return;
  const r=DB.restos.find(x=>x.id===cartRestoId); if(!r) return;
  const sub=cart.reduce((s,i)=>s+(i.price*i.qty),0);
  const order={
    id:'ORD-'+Date.now().toString().slice(-6),
    customer:currentUser?.name||'Guest',
    restoId:r.id, restoName:r.name,
    items:[...cart],
    total:(sub+2).toFixed(2),
    commission:r.commission,
    status:'pending',
    time:new Date().toLocaleTimeString('en-US',{hour:'2-digit',minute:'2-digit'}),
    date:new Date().toLocaleDateString()
  };
  DB.orders.unshift(order); saveDB();
  cart=[]; cartRestoId=null; updateCartUI(); toggleCart();
  notify('🎉',`Order ${order.id} placed!`);
  setTimeout(()=>custPanel('myorders',null),700);
}
function renderCustOrders(){
  const el=document.getElementById('cust-orders-list');
  const myOrders=DB.orders.filter(o=>o.customer===currentUser?.name);
  if(!myOrders.length){el.innerHTML=`<div style="text-align:center;padding:80px;color:var(--gray)"><div style="font-size:48px;margin-bottom:14px">📋</div><div style="font-family:var(--font-head);font-size:24px;font-weight:700;margin-bottom:8px">No orders yet</div><div>Your orders will appear here</div></div>`;return;}
  el.innerHTML=myOrders.map(o=>`
    <div class="order-card">
      <div class="order-head">
        <div><div class="order-id">${o.id}</div><div class="order-time">${o.restoName} · ${o.date} ${o.time}</div></div>
        <span class="tag tag-${statusColor(o.status)}">${o.status}</span>
      </div>
      <div class="order-body">
        ${o.items?.map(i=>`<div class="order-item-row"><span>${i.emoji} ${i.name} ×${i.qty}</span><span>$${(i.price*i.qty).toFixed(2)}</span></div>`).join('')||''}
        <div class="order-total-line"><span>Total</span><span style="color:var(--gold)">$${o.total}</span></div>
        <button class="receipt-btn" onclick="openReceipt('${o.id}')">🖨️ Receipt</button>
      </div>
    </div>
  `).join('');
}

// ═══════════════════════════════════════
// RECEIPT & PRINT
// ═══════════════════════════════════════
function openReceipt(id){
  const o=DB.orders.find(x=>x.id===id); if(!o) return;
  document.getElementById('receiptContent').innerHTML=buildReceipt(o);
  openModal('receiptModal');
}
function buildReceipt(o){
  return`<div class="receipt-paper">
    <div class="rp-logo">EATLAOS</div>
    <div class="rp-sub">${o.restoName||'Restaurant'} · Vientiane</div>
    <hr class="rp-div">
    <div class="rp-row"><span>Order:</span><span><b>${o.id}</b></span></div>
    <div class="rp-row"><span>Date:</span><span>${o.date} ${o.time}</span></div>
    <div class="rp-row"><span>Customer:</span><span>${o.customer}</span></div>
    <div class="rp-row"><span>Restaurant:</span><span>${o.restoName}</span></div>
    <hr class="rp-div">
    ${o.items?.map(i=>`<div class="rp-row"><span>${i.emoji} ${i.name} ×${i.qty}</span><span>$${(i.price*i.qty).toFixed(2)}</span></div>`).join('')||''}
    <hr class="rp-div">
    <div class="rp-row"><span>Subtotal</span><span>$${(parseFloat(o.total)-2).toFixed(2)}</span></div>
    <div class="rp-row"><span>Delivery</span><span>$2.00</span></div>
    <div class="rp-total"><span>TOTAL</span><span>$${o.total}</span></div>
    <div class="rp-row" style="color:#888;font-size:10px"><span>Platform commission (${o.commission}%)</span><span>$${(parseFloat(o.total)*(o.commission/100)).toFixed(2)}</span></div>
    <hr class="rp-div">
    <div class="rp-foot">Thank you for ordering! 🙏<br>eatlaos.la · Vientiane, Laos</div>
  </div>`;
}
function printReceipt(){
  const c=document.getElementById('receiptContent').innerHTML;
  const pa=document.getElementById('printArea');
  pa.innerHTML=c; pa.style.display='block';
  window.print();
  setTimeout(()=>pa.style.display='none',500);
}

// ═══════════════════════════════════════
// SOUND & POLLING
// ═══════════════════════════════════════
function toggleSound(){
  soundEnabled=!soundEnabled;
  const btns=['saSoundBtn','owSoundBtn'];
  btns.forEach(id=>{const el=document.getElementById(id);if(el) el.textContent=soundEnabled?'🔔 Sound ON':'🔕 Sound OFF';});
  notify(soundEnabled?'🔔':'🔕','Sound '+(soundEnabled?'on':'off'));
}
function playSound(){
  if(!soundEnabled) return;
  try{
    const ac=new(window.AudioContext||window.webkitAudioContext)();
    [523,659,784,1047].forEach((f,i)=>{
      const o=ac.createOscillator(),g=ac.createGain();
      o.connect(g);g.connect(ac.destination);
      o.frequency.value=f; o.type='sine';
      g.gain.setValueAtTime(0,ac.currentTime+i*.15);
      g.gain.linearRampToValueAtTime(.25,ac.currentTime+i*.15+.05);
      g.gain.linearRampToValueAtTime(0,ac.currentTime+i*.15+.25);
      o.start(ac.currentTime+i*.15);o.stop(ac.currentTime+i*.15+.3);
    });
  }catch(e){}
}
function startPolling(){
  setInterval(()=>{
    const stored=JSON.parse(localStorage.getItem(KEYS.orders)||'[]');
    if(stored.length>lastOrderCount){
      const n=stored.length-lastOrderCount;
      lastOrderCount=stored.length; DB.orders=stored;
      playSound();
      const al=document.getElementById('newOrderAlert');
      al.textContent=`🔔 ${n} new order${n>1?'s':''} received!`;
      al.classList.add('show'); setTimeout(()=>al.classList.remove('show'),4000);
      if(currentUser?.role==='superadmin'){updateSAStats();renderSARecentOrders();}
      if(currentUser?.role==='owner'){updateOwStats();renderOwRecentOrders();}
    }
  },3000);
}

// ═══════════════════════════════════════
// HELPERS
// ═══════════════════════════════════════
function statusColor(s){return{pending:'orange',preparing:'blue',ready:'green',delivered:'gray',active:'green',inactive:'red',pending_approval:'orange'}[s]||'gray';}
function statusSelect(orderId,current){
  return`<select onchange="updateStatus('${orderId}',this.value)" style="background:var(--surface2);border:1px solid var(--border);color:var(--white);padding:5px 8px;font-family:var(--font-body);font-size:11px;outline:none;">
    ${['pending','preparing','ready','delivered'].map(s=>`<option value="${s}" ${s===current?'selected':''}>${s}</option>`).join('')}
  </select>`;
}
function updateStatus(id,status){
  const o=DB.orders.find(x=>x.id===id); if(!o) return;
  o.status=status; saveDB(); notify('✓',`Order ${id} → ${status}`);
  if(currentUser?.role==='owner') updateOwStats();
}
function openModal(id){document.getElementById(id).classList.add('open');}
function closeModal(id){document.getElementById(id).classList.remove('open');}
let notifT;
function notify(icon,text){
  const el=document.getElementById('notif');
  document.getElementById('notifIcon').textContent=icon;
  document.getElementById('notifText').textContent=text;
  el.classList.add('show'); clearTimeout(notifT);
  notifT=setTimeout(()=>el.classList.remove('show'),3000);
}
function saveSettings(){notify('✓','Settings saved');}
</script>
</body>
</html>
