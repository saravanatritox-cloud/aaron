<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Aaron FIQ · TritoX Quality Check</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;700;900&family=DM+Sans:wght@300;400;500;600;700&display=swap');
:root{
  --bg:#080a0f;--surface:#111520;--surface2:#171c2c;--surface3:#1e2438;
  --border:#252d45;--border2:#2e3850;
  --accent:#00d4ff;--accent2:#0085ff;--accent3:#7b2fff;
  --pass:#00e887;--pass-bg:rgba(0,232,135,0.07);
  --fail:#ff4d6a;--fail-bg:rgba(255,77,106,0.07);
  --warn:#ffb347;--warn-bg:rgba(255,179,71,0.07);
  --stop:#ff2d55;--stop-bg:rgba(255,45,85,0.12);
  --text:#dce4f5;--text2:#8a97bb;--muted:#424d6b;
  --sans:'DM Sans',sans-serif;--brand:'Orbitron',sans-serif;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{background:var(--bg);color:var(--text);font-family:var(--sans);min-height:100vh;
  background-image:radial-gradient(ellipse 80% 40% at 50% -10%,rgba(0,133,255,0.08) 0%,transparent 70%),
  radial-gradient(ellipse 40% 30% at 90% 10%,rgba(123,47,255,0.06) 0%,transparent 60%);}
.app{max-width:1200px;margin:0 auto;padding:36px 24px 80px;}
/* Header */
.brand-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:48px;padding-bottom:28px;border-bottom:1px solid var(--border);}
.brand-left{display:flex;align-items:center;gap:16px;}
.back-link{width:42px;height:42px;border-radius:12px;flex-shrink:0;display:flex;align-items:center;justify-content:center;text-decoration:none;background:var(--surface2);border:1px solid var(--border);color:var(--text);font-size:24px;transition:all .2s;}
.back-link:hover{border-color:var(--accent);color:var(--accent);transform:translateX(-2px);}
.brand-logo{width:64px;height:56px;border-radius:0;flex-shrink:0;background:#fff;display:flex;align-items:center;justify-content:center;box-shadow:none;position:relative;overflow:hidden;}
.brand-logo img{width:100%;height:100%;object-fit:contain;display:block;}
.brand-logo::after{content:none;}
.brand-name{font-family:var(--brand);font-size:22px;font-weight:900;letter-spacing:3px;text-transform:uppercase;background:linear-gradient(90deg,var(--accent),var(--accent2),var(--accent3));-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;}
.brand-sub{font-size:12px;color:var(--text2);letter-spacing:1.5px;text-transform:uppercase;margin-top:3px;}
.brand-right{display:flex;align-items:center;gap:16px;}
.resource-links{display:flex;align-items:center;gap:10px;}
.resource-link{height:40px;display:inline-flex;align-items:center;gap:9px;padding:0 15px;border:1px solid var(--border2);border-radius:10px;background:var(--surface2);color:var(--text);font-size:12px;font-weight:700;text-decoration:none;white-space:nowrap;transition:all .2s;}
.resource-link:hover{border-color:var(--accent);background:rgba(0,133,255,.08);transform:translateY(-1px);}
.resource-icon{width:22px;height:22px;border-radius:6px;display:inline-flex;align-items:center;justify-content:center;color:#fff;font-size:13px;font-weight:900;}
.resource-icon.sheet{background:#188038;}
.resource-icon.doc{background:#1a73e8;}
.status-links{display:flex;align-items:center;gap:10px;}
.brand-badge{background:var(--surface2);border:1px solid var(--border);border-radius:20px;padding:5px 14px;font-size:11px;color:var(--text2);letter-spacing:1px;text-transform:uppercase;}

.debug-btn{background:#1e2438;border:1px solid #2e3850;color:#8a97bb;border-radius:6px;padding:4px 10px;font-size:11px;cursor:pointer;transition:all 0.15s;}
.debug-btn:hover{border-color:#00d4ff;color:#00d4ff;}

.live-dot{display:inline-block;width:6px;height:6px;border-radius:50%;background:var(--pass);margin-right:5px;animation:pulse 1.8s ease-in-out infinite;}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:0.5;transform:scale(0.8)}}
@media(max-width:900px){
  .brand-header{align-items:flex-start;flex-direction:column;}
  .brand-right{width:100%;justify-content:space-between;flex-wrap:wrap;}
}
@media(max-width:560px){
  .resource-links{width:100%;}
  .resource-link{flex:1;justify-content:center;padding:0 10px;}
}
/* Upload */
.upload-zone{border:2px dashed var(--border2);border-radius:20px;padding:56px 32px;text-align:center;cursor:pointer;transition:all 0.25s;background:var(--surface);margin-bottom:32px;position:relative;overflow:hidden;}
.upload-zone::before{content:'';position:absolute;inset:0;background:radial-gradient(ellipse 60% 50% at 50% 100%,rgba(0,133,255,0.04) 0%,transparent 70%);pointer-events:none;}
.upload-zone:hover,.upload-zone.drag-over{border-color:var(--accent2);background:rgba(0,133,255,0.04);}
.upload-zone input{display:none;}
.upload-icon{font-size:42px;margin-bottom:16px;display:block;}
.upload-zone h2{font-size:18px;font-weight:700;margin-bottom:8px;font-family:var(--brand);letter-spacing:1px;}
.upload-zone p{font-size:13px;color:var(--text2);line-height:1.7;}
.btn-upload{display:inline-block;margin-top:20px;padding:12px 32px;background:linear-gradient(135deg,var(--accent2),var(--accent3));color:#fff;border-radius:10px;font-size:14px;font-weight:700;cursor:pointer;border:none;transition:all 0.2s;font-family:var(--sans);letter-spacing:0.5px;box-shadow:0 4px 20px rgba(0,133,255,0.25);}
.btn-upload:hover{transform:translateY(-1px);}
/* Progress */
.progress-wrap{display:none;margin-bottom:28px;}
.progress-label{font-size:12px;color:var(--text2);margin-bottom:10px;display:flex;align-items:center;gap:8px;}
.progress-track{height:4px;background:var(--surface2);border-radius:4px;overflow:hidden;}
.progress-fill{height:100%;width:0%;background:linear-gradient(90deg,var(--accent2),var(--accent),var(--accent3));border-radius:4px;transition:width 0.3s;}
/* Summary */
.summary-bar{display:none;gap:14px;margin-bottom:28px;flex-wrap:wrap;}
.summary-card{flex:1;min-width:130px;background:var(--surface);border:1px solid var(--border);border-radius:14px;padding:18px 20px;}
.sc-label{font-size:10px;color:var(--muted);text-transform:uppercase;letter-spacing:1.2px;margin-bottom:8px;font-weight:600;}
.sc-val{font-family:var(--brand);font-size:30px;font-weight:700;}
.sc-total .sc-val{color:var(--text);}
.sc-pass .sc-val{color:var(--pass);}
.sc-fail .sc-val{color:var(--fail);}
.sc-warn .sc-val{color:var(--warn);}
/* Toolbar */
.toolbar{display:none;gap:8px;margin-bottom:20px;flex-wrap:wrap;align-items:center;}
.filter-btn{padding:7px 18px;border-radius:20px;border:1px solid var(--border);background:var(--surface);color:var(--text2);font-size:13px;cursor:pointer;transition:all 0.15s;font-family:var(--sans);font-weight:500;}
.filter-btn:hover{border-color:var(--accent2);color:var(--text);}
.filter-btn.active{background:linear-gradient(135deg,var(--accent2),var(--accent3));border-color:transparent;color:#fff;}
.search-box{margin-left:auto;padding:8px 16px;border-radius:20px;border:1px solid var(--border);background:var(--surface);color:var(--text);font-size:13px;width:210px;font-family:var(--sans);outline:none;transition:border-color 0.2s;}
.search-box:focus{border-color:var(--accent2);}
.search-box::placeholder{color:var(--muted);}
.btn-action{padding:8px 18px;border-radius:8px;border:1px solid var(--border);background:var(--surface2);color:var(--text2);font-size:13px;cursor:pointer;transition:all 0.15s;font-family:var(--sans);font-weight:500;}
.btn-action:hover{border-color:var(--accent2);color:var(--text);}
.btn-stop{border-color:rgba(255,45,85,0.4);color:var(--stop);}
.btn-stop:hover{border-color:var(--stop);background:var(--stop-bg);}
.btn-danger:hover{border-color:var(--fail);color:var(--fail);}
/* Table */
.results-wrap{display:none;}
.results-table{width:100%;border-collapse:collapse;}
.results-table thead tr{border-bottom:1px solid var(--border);}
.results-table th{text-align:left;padding:10px 14px;font-size:10px;text-transform:uppercase;letter-spacing:1px;color:var(--muted);font-weight:700;white-space:nowrap;}
.row-card{border-bottom:1px solid var(--border);transition:background 0.15s;}
.row-card:hover{background:rgba(255,255,255,0.015);}
/* STOP banner */
.stop-banner{background:var(--stop-bg);border-left:3px solid var(--stop);padding:8px 14px;font-size:12px;font-weight:700;color:var(--stop);letter-spacing:0.5px;}
.row-main{display:grid;grid-template-columns:32px 200px 140px 90px 110px 105px 1fr;align-items:center;gap:0;cursor:pointer;padding:15px 14px;}
.row-expand{color:var(--muted);font-size:10px;transition:transform 0.2s;user-select:none;}
.row-expand.open{transform:rotate(90deg);color:var(--accent);}
.col-name .cname{font-weight:600;font-size:14px;color:var(--text);}
.col-name .fname{font-size:11px;color:var(--muted);margin-top:3px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;max-width:190px;}
.chip{display:inline-block;padding:3px 10px;border-radius:20px;font-size:11px;font-weight:700;}
.chip-farmers{background:rgba(255,179,71,0.12);color:var(--warn);border:1px solid rgba(255,179,71,0.2);}
.chip-farmer-bristol{background:rgba(123,47,255,0.12);color:#a78bfa;border:1px solid rgba(123,47,255,0.2);}
.chip-bristol{background:rgba(0,133,255,0.12);color:var(--accent);border:1px solid rgba(0,133,255,0.2);}
.chip-unknown{background:var(--surface2);color:var(--muted);border:1px solid var(--border);}
.status-badge{display:inline-flex;align-items:center;gap:5px;padding:5px 13px;border-radius:20px;font-size:12px;font-weight:700;}
.status-pass{background:var(--pass-bg);color:var(--pass);border:1px solid rgba(0,232,135,0.2);}
.status-fail{background:var(--fail-bg);color:var(--fail);border:1px solid rgba(255,77,106,0.2);}
.status-warn{background:var(--warn-bg);color:var(--warn);border:1px solid rgba(255,179,71,0.2);}
.err-pill{display:inline-block;background:var(--fail-bg);color:var(--fail);border:1px solid rgba(255,77,106,0.2);border-radius:5px;padding:2px 9px;margin:2px 3px 2px 0;font-size:11px;}
.warn-pill{display:inline-block;background:var(--warn-bg);color:var(--warn);border:1px solid rgba(255,179,71,0.2);border-radius:5px;padding:2px 9px;margin:2px 3px 2px 0;font-size:11px;}
.ok-text{color:var(--pass);font-size:12px;font-weight:600;}
/* Details */
.row-details{display:none;padding:4px 14px 22px 46px;gap:10px;}.row-details.open{display:flex;flex-direction:column;}.row-details-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(230px,1fr));gap:10px;}

.detail-group{background:var(--surface2);border:1px solid var(--border);border-radius:12px;padding:16px;}
.detail-group h4{font-size:10px;text-transform:uppercase;letter-spacing:1px;color:var(--muted);margin-bottom:12px;font-weight:700;padding-bottom:8px;border-bottom:1px solid var(--border);}
.check-item{display:flex;align-items:flex-start;gap:8px;margin-bottom:8px;font-size:13px;line-height:1.4;}
.check-item:last-child{margin-bottom:0;}
.ci-icon{flex-shrink:0;margin-top:1px;}
.ci-ok{color:var(--pass);}.ci-fail{color:var(--fail);}.ci-warn{color:var(--warn);}.ci-info{color:var(--accent);}
.ci-label{color:var(--text2);flex:1;}
.ci-val{font-size:12px;font-weight:600;white-space:nowrap;}
.ci-val.bad{color:var(--fail);}.ci-val.ok{color:var(--pass);}.ci-val.neutral{color:var(--text2);}
.vehicle-item{background:var(--surface3);border-radius:8px;padding:11px 13px;margin-bottom:7px;}
.vehicle-item:last-child{margin-bottom:0;}
.v-name{font-weight:600;font-size:13px;margin-bottom:6px;color:var(--text);}
.v-tags{display:flex;flex-wrap:wrap;gap:4px;margin-bottom:5px;}
.v-ok{background:var(--pass-bg);color:var(--pass);padding:2px 9px;border-radius:4px;font-size:11px;font-weight:600;}
.v-fail{background:var(--fail-bg);color:var(--fail);padding:2px 9px;border-radius:4px;font-size:11px;font-weight:600;}
.v-info{background:rgba(0,133,255,0.1);color:var(--accent);padding:2px 9px;border-radius:4px;font-size:11px;font-weight:600;}
.spinner{display:inline-block;width:13px;height:13px;border:2px solid var(--border2);border-top-color:var(--accent);border-radius:50%;animation:spin 0.7s linear infinite;vertical-align:middle;}
@keyframes spin{to{transform:rotate(360deg)}}
.processing-row td{padding:16px 14px;color:var(--text2);font-size:13px;border-bottom:1px solid var(--border);}

/* AgencyZoom Checklist */
.az-panel{background:var(--surface2);border:1px solid var(--border);border-radius:12px;padding:16px;margin-top:4px;}
.az-panel h4{font-size:10px;text-transform:uppercase;letter-spacing:1px;color:var(--accent);margin-bottom:12px;font-weight:700;padding-bottom:8px;border-bottom:1px solid var(--border);display:flex;align-items:center;gap:6px;}
.az-row{display:flex;align-items:center;gap:8px;margin-bottom:7px;font-size:13px;}
.az-row:last-child{margin-bottom:0;}
.az-label{color:var(--text2);flex:1;min-width:120px;font-size:12px;}
.az-val{color:var(--text);font-weight:600;flex:2;font-size:13px;}
.az-copy{background:var(--surface3);border:1px solid var(--border2);color:var(--accent);border-radius:6px;padding:3px 10px;font-size:11px;cursor:pointer;transition:all 0.15s;white-space:nowrap;font-family:var(--sans);}
.az-copy:hover{background:rgba(0,212,255,0.1);border-color:var(--accent);}
.az-copy.copied{background:rgba(0,232,135,0.1);border-color:var(--pass);color:var(--pass);}
.az-manual{color:var(--warn);font-size:11px;font-style:italic;}
/* Home QC group */
.home-group{border-color:rgba(123,47,255,0.3);}
.home-group h4{color:#a78bfa;}


/* Bookmarklet Section */
.bm-section{background:linear-gradient(135deg,rgba(0,212,255,0.05),rgba(123,47,255,0.05));border:1px solid rgba(0,212,255,0.2);border-radius:16px;padding:20px 24px;margin:24px 0 8px 0;}
.bm-section h3{font-family:var(--mono);color:var(--accent);font-size:13px;margin-bottom:6px;letter-spacing:1px;}
.bm-section p{color:var(--text2);font-size:12px;margin-bottom:14px;line-height:1.6;}
.bm-steps{display:flex;gap:12px;flex-wrap:wrap;margin-bottom:16px;}
.bm-step{background:var(--surface2);border:1px solid var(--border);border-radius:10px;padding:10px 14px;font-size:12px;color:var(--text2);flex:1;min-width:140px;}
.bm-step strong{display:block;color:var(--text);margin-bottom:3px;font-size:13px;}
.bm-drag-wrap{display:flex;align-items:center;gap:12px;flex-wrap:wrap;}
.bm-drag-btn{display:inline-block;background:linear-gradient(135deg,#00d4ff,#7b2fff);color:#fff;font-weight:700;font-size:13px;padding:10px 20px;border-radius:10px;text-decoration:none;cursor:grab;white-space:nowrap;box-shadow:0 4px 15px rgba(0,212,255,0.3);}
.bm-drag-btn:hover{opacity:0.9;}
.bm-drag-hint{color:var(--text2);font-size:12px;}
.bm-status{margin-top:10px;font-size:12px;padding:8px 12px;border-radius:8px;display:none;}
.bm-status.ready{display:block;background:rgba(0,232,135,0.1);border:1px solid var(--pass);color:var(--pass);}

.footer{margin-top:60px;padding-top:24px;border-top:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px;}
.footer-brand{font-family:var(--brand);font-size:13px;letter-spacing:2px;background:linear-gradient(90deg,var(--accent),var(--accent3));-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;}
.footer-note{font-size:12px;color:var(--muted);}
</style>
</head>
<body>
<div class="app">
  <div class="brand-header">
    <div class="brand-left">
      <a class="back-link" href="https://tritoxtech.github.io/Qccheck/" aria-label="Back to FIQ project selection" title="Back to FIQ project selection">&#8592;</a>
      <div class="brand-logo"><img src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBxAQDw8OEBAVEBAWEBIaEBUWDQ8QEBARIBciIiAdHxkaKDQgJCYxIBgZJDIkMSstMC8vIys0Pz8uNzQtLy0BCgoKDg0OGhAQGjceHx0tLS0rKy03LTMtMCstLS0tLy0tLjctLTc3NzU2NjAtNTMtMi8tNy03Ky0rKzc0LSs3K//AABEIAMgAyAMBIgACEQEDEQH/xAAbAAEAAgMBAQAAAAAAAAAAAAAABQYBBAcCA//EAD0QAAICAAMFAwkGBQQDAAAAAAABAgMEBRESITFBUQYTcQciMkJhgZGhsRQzUqLB0RUjcpKyQ2KC4RYkU//EABoBAQADAQEBAAAAAAAAAAAAAAADBAUCBgH/xAAkEQEAAgEEAQQDAQAAAAAAAAAAAQIDBBESMSEFE0FRImFxMv/aAAwDAQACEQMRAD8A7iAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMFI7YZjbRi47EnsOqLa2mk3tNfoXcoflGh/Nol1hJfB/9kOf/Dm/T1g84lYtY2z15pzlqjaWYXf/AEl/cyjwm4tNPRrg0WXLrZygnNaPl7V1M202r1KDeUss0vX+o/kelm9/4/yw/Y0UR+d5vTg6ZX3S0ivRS02py5JLqfIyZJnaJk5Sks47XvCUyvusSiuHmx2py5JLmx5N+1+IzGNzxNMad+1h9G9Z1cN6fTdv568OvLMNRdmlyxuLWzQn/wCvTv2dOr/fn4HQOy+I7vFU8k3sv37l89Dd02jvGKb3nyinVRW8Ujz9ulgAjXgAAAAAAAAAAAAAAAAAAAABgpnlHh5uGl7bF8dP2LmVntNZXa4V6bWxPX2a6aaEGe0RSd3N+lSyvLddLLFu9VdfayZRkjO0GeU4Kl3XP2QitNuyXRGX5vO0K/b1nuc04KmV90tFwjFabdkuiRz7B4a7M71jsYtKF9xTv2dn9vbz8DzgcJdmdyx2N3VL7irfs7Ph0+vgW5LTcuB6H0/0+KRzv2q6jUcfxr2JacNyPcJuLTW5p6rxPDkuGu/l7TKNrbxsz9/l1nC3KyuFi4SjFr3o+pB9jsTt4WK5wlKL+q+TJ0wb142mHocduVYt9gAOXYAAAAAAAAAAAAAAADAMkDnGa8a63/VJfREeTJFI3l8mdmc4zXTWut7/AFpLl7EQQIftL2hpwNPeWPWb1Vdaa2rJft1Zl3vbLZXmZtL32jz6nA0u216t7q4Jrbsl0X6vkc+y7A35ld9vx33f+jVv2dnlu/D9TzlmXXZhf/EMdvg/uq9+zs8t3KP1+tySN/0/0+KRzv2qajUcfxp2JbtFuNPNszrw1Tttei9VetOXRDNszrwtTttei9VL0py6IjOznZ+zMLY5hjo6VLfh6HrsuPJtdP8ALwLur1dcFf2j0mktnt+lfrxuLWNwWMxMXXVdKUaYt7lW9Fw/5J68/gdAI3yrYJywULorfTdF6rlF7vrsGzl2JVtNVq9eEX4NreQ+m6ictZme0/qWnjFMbdLn2DxOlltT5xUl4rc/qvgXQ5l2exPd4qmXLa0fg936nTTjWV2yb/aTRX3x7fTIAKi6AAAAAAAAAAAAAMAFZzPtDVOdmGptjKcJONqVkXOLXFacUR5LxSu8vkztDZzjNeNdb/qkvoiDBgycmSck7yr2tMobtT2jqwFPeT86x6qqtPfN/ourOb5LV/EsTLGYy2MnteZVtrlwWzyiunP69dxGHhZHZshGyPNSipR+DK5mHYLL7m2qnTLrVNw/Lvj8i1pM+PFO9o3fJ81mInZ6SMWTUU2+CIi3sVjqd+Ex7kuULotpezXevkjUxF+bUpwvwPfJrSMqnte9pa/oegx+pYbx3soTo7b+J3aeQzw+OzG2eMsTVUmsNS09iaT4vrw105+COpQkmk001y04HGMm7C466alqqNHq5ym9qL93Mv8A5P8AOZ4im2m5JXUWbE9PW46P4qXwMDWT7lpvFt3odHatY4Qm89wP2jC4ijnOqaj/AFabvnoUXsBitvCd2+Nc5R09j3r6v4HSDmWUQ+zZtj8JwjN7cF+ZL4TfwLPpGXjk4/aD1XHyxb/S1p6bzquXYjvaq7PxQi346bzlJJ4PtddhYwq7uNlST011jNb+v/Rt6zHN6xMfDG0WWKWmJ+XTAVHBdvsLPRWRnS+b024fFb/kWDBZth7vuroTfRTW1/bxMyazHcNWMlbdS3gAcuwAAAAAAAAAAYOWdsvJzhLMTZiI95TbZOU9uFj32N6t6PXm+Wh1M0s2wve1NJect8fHoRZazNfx7d45jl56cY/gmc4XfhsasVBcIW+k/Z52v+SMf+b4vDbswy+da52V6uHz3fmL2zDRme7E/wCo3Wb6Sluleyzttl9+ml6rl+G1Op/F7vmWGqxSSlFqSfBpppkLmfZTA4jXvMPBS/FBOufxjpr7yv2+T6dLcsBjbcO/wuTcX746fRjbHbqdlW2jtHS+AoCxefYT7yqGNrXOKTn7tnR/lZ98J5SaNru8VRbhZ89YucV9JfIezbuPP8V7Yr17heChZWvsmf4ml7oYmvbh7Z+l9VYWzLs9wuI07nEV2N+qppT/ALXvKn5RV3GKy7MVuULdmx/7ddUvhtn3FE7zWfmHWG3G8SvZzvt7D7PmeAxi3Rmtib8Ho3/bZ8joieu9cCo+VHA95l8rEvOqshJddH5r/wAtfcdaW/DLEtPUU545htmrmENYp9GeMmxffYem7nKuLf8AVz+eptyimtHvR7P/AFX+vHbcbfxEV1SlwWpLZRkruthXrrJvfx0iubPSWm5bi/8AZLKe5q72S/mTXvjDkv1Kubjipv8AKzgi2W+3wm8LQq4RrjrpFJLV6s+wBltmAAAAAAAAAAAAABVM8wnd2OS9GW9ex80Rxb81wne1OPrLfHxKfY9E/YjK1OPhfeOpX8F+Vf4xCxPg0/BpnoqcZNPVPR+83cJjb3JQhrOTekY7O02V4jdNunz4YvB1XLZtrhZHpOEZr5k1hshudalNxjZzjxS958L8ruhxg2usfOJZxZK+dkfuUnxuomZeTrAW6ygp0S/2Tbjr4S1+WhA5x2GzJ19zXi/tNCknGuc5Raa4aJ6pceqOmtdTB9rnvX9vlsNJ+Gh2fVqwmHjdFwtjVGM02m9pbtd3XTX3n2zTBq+i6h8J1zj4aribJki5fluk28bOaeT/ABTlh50vdKqxrTonv+u0WgquFh9lzrGYfhG3Wcfa3536zRb8Jh5W2QrgtZSeiPZaTLFsMWeR1mKa5piPlL9lMp7+3bkv5cGm+kpckdCNTK8DHD1Rqjy4vnKXNm4Z+fL7lt/hp6fD7dNvkABCnAAAAAAAAAAAAAGCr9o8tlGNt0FrFxk2lxi9N5aAR5McXjaXdLzSd4cjyvLLcTPYqjr+KT9CK9rOi5FkNWFjqlt2P0ptb/BdESdNEIJqEVBNtvZio6vruPoR4tPWnnuXWTLNv4yACwifG7DQn6UVLxSI+/Iapei3B+Oq+ZKmTi2Otu4dRe0dSrF+RWx9Fqa8dl/Mj7sPOHpRcfFMu55a13Fe2jrPXhNXU2jtwHyjQ7jHYDHLhrsT8E9fpOXwOsdjcp2IfaZrz5rzPZDr7yWxOSYWyddllEJyrntV6wTUZ6aa6cNd5IlvDa2PF7arlpW+T3GQAHQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAD/2Q==" alt="TritoX logo"/></div>
      <div>
        <div class="brand-name">TritoX</div>
        <div class="brand-sub">Aaron FIQ · Quality Check System · Auto &amp; Bundle</div>
      </div>
    </div>
    <div class="brand-right">
      <nav class="resource-links" aria-label="Project resources">
        <a class="resource-link" href="https://docs.google.com/spreadsheets/d/1q-d_-eAQcNXTOkqlLUoG2kv6AoE2MUb76PPY2ScvC6Q/edit?usp=drivesdk" target="_blank" rel="noopener noreferrer">
          <span class="resource-icon sheet" aria-hidden="true">▦</span>
          <span>Open Worksheet</span>
        </a>
        <a class="resource-link" href="https://docs.google.com/document/d/15ZswbwNtzZBtdaD3tD5PUD7BSwdddlOtU78LtX509gM/edit?tab=t.0" target="_blank" rel="noopener noreferrer">
          <span class="resource-icon doc" aria-hidden="true">≡</span>
          <span>View Instructions</span>
        </a>
      </nav>
      <div class="status-links">
        <span class="brand-badge"><span class="live-dot"></span>Live</span>
        <span class="brand-badge">v4.0</span>
      </div>
    </div>
  </div>

  <div class="upload-zone" id="uploadZone">
    <input type="file" id="fileInput" multiple accept=".pdf"/>
    <span class="upload-icon">📂</span>
    <h2>Drop Quote PDFs Here</h2>
    <p>Farmers · Farmer-Bristol · Bristol West · Bulk upload up to 20 PDFs</p>
    <button class="btn-upload" onclick="document.getElementById('fileInput').click()">Select PDF Files</button>
  </div>

  <div class="progress-wrap" id="progressWrap">
    <div class="progress-label"><span class="spinner"></span><span id="progressLabel">Processing...</span></div>
    <div class="progress-track"><div class="progress-fill" id="progressFill"></div></div>
  </div>

  <div class="summary-bar" id="summaryBar">
    <div class="summary-card sc-total"><div class="sc-label">Total</div><div class="sc-val" id="scTotal">0</div></div>
    <div class="summary-card sc-pass"><div class="sc-label">✓ Passed</div><div class="sc-val" id="scPass">0</div></div>
    <div class="summary-card sc-fail"><div class="sc-label">✗ Flagged</div><div class="sc-val" id="scFail">0</div></div>
    <div class="summary-card sc-warn"><div class="sc-label">⚠ Warnings</div><div class="sc-val" id="scWarn">0</div></div>
  </div>

  <div class="toolbar" id="toolbar">
    <button class="filter-btn active" data-filter="all">All</button>
    <button class="filter-btn" data-filter="fail">Flagged</button>
    <button class="filter-btn" data-filter="warn">Warnings</button>
    <button class="filter-btn" data-filter="pass">Passed</button>
    <input class="search-box" id="searchBox" type="text" placeholder="🔍  Search customer name..."/>
    <button class="btn-action" onclick="exportCSV('all')">⬇ Export All</button>
    <button class="btn-action btn-stop" onclick="exportCSV('flagged')">⬇ Export Flagged</button>
    <button class="btn-action btn-danger" onclick="clearAll()">🗑 Clear All</button>
  </div>

  <div class="results-wrap" id="resultsWrap">
    <table class="results-table">
      <thead>
        <tr>
          <th></th><th>Customer</th><th>Type</th><th>Vehicles</th>
          <th>Monthly EFT</th><th>Status</th><th>Flags / Notes</th>
        </tr>
      </thead>
      <tbody id="resultsBody"></tbody>
    </table>
  </div>

  <div id="bmSection" class="bm-section" style="display:none;">
  <h3>📋 AgencyZoom Fill + Attach PDF — <span id="bmName">Processing...</span></h3>
  <div id="bmReady" class="bm-status ready" style="display:none;"></div>
  <div class="bm-steps" style="margin-top:12px;">
    <div class="bm-step"><strong>Step 1</strong>Process PDF in TritoX ✅</div>
    <div class="bm-step"><strong>Step 2</strong>Open correct lead in AgencyZoom</div>
    <div class="bm-step"><strong>Step 3</strong>Click "🚀 Fill + Attach PDF" in AgencyZoom</div>
    <div class="bm-step"><strong>Step 4</strong>Check the filled fields and attached PDF before saving.</div>
  </div>
  <div style="margin-top:14px;padding:12px 16px;background:rgba(0,232,135,0.05);border:1px solid rgba(0,232,135,0.2);border-radius:10px;">
    <div style="color:var(--pass);font-size:12px;font-weight:700;margin-bottom:6px;">📦 Tampermonkey Setup (One Time Only)</div>
    <div style="color:var(--text2);font-size:12px;line-height:1.8;">
      1. Install <strong style="color:var(--text)">Tampermonkey</strong> from Chrome Web Store (free)<br>
      2. Click <strong style="color:var(--text)">"Get Tampermonkey Script"</strong> button below<br>
      3. Copy the script → replace your existing TritoX AgencyZoom script in Tampermonkey → Save<br>
      4. Refresh Aaron QC and AgencyZoom, then select your PDF again.
    </div>
    <button onclick="showTMScript()" style="margin-top:10px;background:linear-gradient(135deg,#00d4ff,#7b2fff);color:#fff;border:none;border-radius:8px;padding:8px 18px;font-size:12px;font-weight:700;cursor:pointer;">📜 Get Tampermonkey Script</button>
  </div>
</div>
<div class="footer">
    <span class="footer-brand">TRITOX QC</span>
    <span class="footer-note">All checks run locally · No data uploaded · Free to use forever</span>
  </div>
</div>

<script>
pdfjsLib.GlobalWorkerOptions.workerSrc='https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
let allResults=[];
let activeFilter='all';

const zone=document.getElementById('uploadZone');
zone.addEventListener('click',e=>{if(e.target.tagName!=='BUTTON')document.getElementById('fileInput').click();});
zone.addEventListener('dragover',e=>{e.preventDefault();zone.classList.add('drag-over');});
zone.addEventListener('dragleave',()=>zone.classList.remove('drag-over'));
zone.addEventListener('drop',e=>{e.preventDefault();zone.classList.remove('drag-over');handleFiles(e.dataTransfer.files);});
document.getElementById('fileInput').addEventListener('change',e=>{handleFiles(e.target.files);e.target.value='';});
document.getElementById('searchBox').addEventListener('input',renderTable);
document.querySelectorAll('.filter-btn').forEach(btn=>{
  btn.addEventListener('click',()=>{
    document.querySelectorAll('.filter-btn').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');activeFilter=btn.dataset.filter;renderTable();
  });
});

async function handleFiles(files){
  const pdfs=Array.from(files).filter(f=>f.name.toLowerCase().endsWith('.pdf'));
  if(!pdfs.length)return;
  document.getElementById('progressWrap').style.display='block';
  document.getElementById('summaryBar').style.display='flex';
  document.getElementById('toolbar').style.display='flex';
  document.getElementById('resultsWrap').style.display='block';
  const fill=document.getElementById('progressFill');
  const label=document.getElementById('progressLabel');
  for(let i=0;i<pdfs.length;i++){
    label.textContent=`Processing ${i+1} of ${pdfs.length}: ${pdfs[i].name}`;
    fill.style.width=((i/pdfs.length)*100)+'%';
    const spinId='spin_'+Date.now();
    const tbody=document.getElementById('resultsBody');
    const spinRow=document.createElement('tr');
    spinRow.id=spinId;spinRow.className='processing-row';
    spinRow.innerHTML=`<td colspan="7"><span class="spinner"></span>&nbsp;Analyzing ${pdfs[i].name}...</td>`;
    tbody.appendChild(spinRow);
    try{
      const text=await extractPDFText(pdfs[i]);
      const result=analyzeQuote(text,pdfs[i].name);
      result._rawText=text;
      allResults.push(result);
      saveToLocalStorage(result);
    }catch(e){
      allResults.push({filename:pdfs[i].name,name:pdfs[i].name.replace('.pdf','').replace(/_/g,' '),
        quoteType:'Unknown',errors:['Could not read PDF'],warnings:[],vehicles:[],drivers:[],
        status:'fail',monthlyEFT:null,vehicleCount:0,putInStop:false,checks:{}});
    }
    document.getElementById(spinId)?.remove();
    renderTable();updateSummary();
  }
  fill.style.width='100%';
  label.textContent=`✓ Done! Processed ${pdfs.length} quote${pdfs.length>1?'s':''}.`;
  setTimeout(()=>{document.getElementById('progressWrap').style.display='none';},2500);
  // Show bookmarklet section
  const bmSec=document.getElementById('bmSection');
  if(bmSec){bmSec.style.display='block';}
  initBookmarklet();
}

async function extractPDFText(file){
  const buf=await file.arrayBuffer();
  const pdf=await pdfjsLib.getDocument({data:buf}).promise;
  let text='';
  for(let p=1;p<=pdf.numPages;p++){
    const page=await pdf.getPage(p);
    const content=await page.getTextContent();
    let pageText='';
    let lastY=null;
    for(const item of content.items){
      if(lastY!==null&&Math.abs(item.transform[5]-lastY)>5) pageText+='\n';
      pageText+=item.str+' ';
      lastY=item.transform[5];
    }
    text+=pageText+'\n';
  }
  return text;
}

// ── MAIN ANALYSIS ──
function analyzeQuote(text,filename){
  const t=text;
  const errors=[];
  const warnings=[];

  // ── Carrier & Quote Type Detection ──
  const isBristolSummary=/Bristol\s+West\s+Auto\s+quote\s+summary/i.test(t);
  const isFarmersSummary=/Farmers\s+Auto\s+quote\s+summary/i.test(t);
  const hasFarmersHome=/Auto\/Farmers\s+Home/i.test(t);
  const hasAutoHomeCondo=/Auto\/Home\s+or\s+Condo/i.test(t);

  let quoteType='Unknown';
  if(isFarmersSummary) quoteType='Farmers';
  else if(isBristolSummary&&hasFarmersHome) quoteType='Farmer-Bristol';
  else if(isBristolSummary) quoteType='Bristol West';

  // ── Customer Name ──
  const nameMatch=t.match(/Prepared\s+for\s+\n?\s*([A-Z][a-zA-Z\s]+?)(?:\n|Effective|Quote number)/);
  const name=nameMatch?nameMatch[1].trim().split('\n')[0].trim():filename.replace(/\.pdf$/i,'').replace(/_/g,' ');

  // ── Monthly EFT — AUTO ONLY ──
  // Rules:
  // 1) Read only from the Auto quote details section.
  // 2) Stop before any Home quote summary/details section so Home EFT can never bleed into Auto.
  // 3) If Auto explicitly uses Monthly EFT, capture the installment amount.
  // 4) If Auto is Paid in full / 1 Pay only, keep monthlyEFT=null and show the BW warning.
  const autoSectionIdxEarly=t.search(/Auto\s+quote\s+details/i);

  // Find the FIRST Home section that occurs after Auto details (summary OR details).
  let homeSectionIdxEarly=-1;
  if(autoSectionIdxEarly>-1){
    const afterAuto=t.substring(autoSectionIdxEarly);
    const relHomeIdx=afterAuto.search(/(?:Farmers\s+)?Home\s+quote\s+(?:summary|details)/i);
    if(relHomeIdx>-1) homeSectionIdxEarly=autoSectionIdxEarly+relHomeIdx;
  }

  let autoChunkEarly='';
  if(autoSectionIdxEarly>-1){
    const autoEnd=homeSectionIdxEarly>autoSectionIdxEarly?homeSectionIdxEarly:t.length;
    autoChunkEarly=t.substring(autoSectionIdxEarly,autoEnd);
  }

  let monthlyEFT=null;

  // Method 1 — Options summary, e.g.:
  // Installments $1,420.88/mo
  // Pay plan Monthly EFT
  const autoOptionEFT=autoChunkEarly.match(
    /Installments\s+\$?([\d,]+(?:\.\d{1,2})?)\s*\/?\s*mo[\s\S]{0,180}?Pay\s+plan\s+Monthly\s+EFT/i
  );
  if(autoOptionEFT){
    monthlyEFT=parseFloat(autoOptionEFT[1].replace(/,/g,''));
  }

  // Method 2 — Payment plans table, e.g.:
  // Monthly EFT $1,353.70 $1,420.88 $8,398.10
  //                  due today   installment   total
  if(monthlyEFT===null){
    const autoPaymentTableEFT=autoChunkEarly.match(
      /Monthly\s+EFT\s+\$?([\d,]+(?:\.\d{1,2})?)\s+\$?([\d,]+(?:\.\d{1,2})?)(?:\s+\$?[\d,]+(?:\.\d{1,2})?)?/i
    );
    if(autoPaymentTableEFT){
      monthlyEFT=parseFloat(autoPaymentTableEFT[2].replace(/,/g,''));
    }
  }

  const eftAutoMissing=monthlyEFT===null||Number.isNaN(monthlyEFT);
  if(eftAutoMissing){
    monthlyEFT=null;
    errors.push('Monthly EFT auto not available — use BW');
  }

  // ── Dates — exactly 14 days from Prepared on ──
  const prepMatch=t.match(/Prepared\s+on\s+(\d{2}\/\d{2}\/\d{2,4})/i);
  const startMatch=t.match(/Policy\s+start\s+date\s+\n?\s*([A-Za-z]+\s+\d+,?\s*\d{4})/i);
  let dateOk=false,dateDiff=null,prepDateStr='',startDateStr='';
  if(prepMatch&&startMatch){
    prepDateStr=prepMatch[1];startDateStr=startMatch[1].trim();
    const pd=parseDate(prepDateStr);
    const sd=parseDate(startDateStr);
    if(pd&&sd){
      dateDiff=Math.round((sd-pd)/(1000*60*60*24));
      dateOk=(dateDiff===14);
      if(dateDiff<14) errors.push(`Policy start date too soon (${dateDiff} days — must be exactly 14)`);
      else if(dateDiff>14) errors.push(`Policy start date too far out (${dateDiff} days — must be exactly 14)`);
    }
  } else warnings.push('Could not verify policy start date');

  // ── Vehicles & Drivers ──
  const vehicles=extractVehicles(t);
  const vehicleCount=vehicles.length;
  const drivers=extractDrivers(t);

  // PIP age check handled after carrier checks below

  // ── Run type-specific checks ──
  if(quoteType==='Farmers') checkFarmers(t,errors,warnings,vehicles);
  else if(quoteType==='Farmer-Bristol') checkFarmerBristol(t,errors,warnings,vehicles);
  else if(quoteType==='Bristol West') checkPureBristol(t,errors,warnings,vehicles);
  else warnings.push('Quote type could not be identified');

  // ── Premium Threshold ──
  // Monthly EFT limits apply ONLY to 1, 2, or 3 vehicles.
  // 1 vehicle = $400 max, 2 vehicles = $800 max, 3 vehicles = $1,000 max.
  // 4+ vehicles = NO Monthly EFT threshold / never PUT IN STOP for premium amount.
  let premiumOk=true,putInStop=false;
  if(monthlyEFT!==null && vehicleCount>=1 && vehicleCount<=3){
    const limits={1:400,2:800,3:1000};
    const limit=limits[vehicleCount];
    if(monthlyEFT>limit){
      premiumOk=false;
      putInStop=true;
      errors.push(`⛔ PUT IN STOP — Monthly EFT $${monthlyEFT.toFixed(2)} exceeds $${limit} limit for ${vehicleCount} vehicle${vehicleCount>1?'s':''}`);
    }
  }

  // ── PIP Age-Based Check ──
  // All drivers 65+ → must be Opt.6
  // Any driver 64 or below → must be Opt.3
  const pipLine2=t.match(/Personal\s+(?:Injury\s+)?[Pp]rotection\s+[Mm]edical\s+(Opt\.[\s\S]{0,80}?)(?=PIP\s+medical|PIP\s+wage|Work\s+loss)/i);
  const pipStr2=pipLine2?pipLine2[1].trim():'';
  const hasOpt3=/Opt\.\s*3/i.test(pipStr2);
  const hasOpt6=/Opt\.\s*6/i.test(pipStr2);
  if(drivers.length>0){
    const allOver65=drivers.every(d=>d.age>=65);
    const anyUnder65=drivers.some(d=>d.age<65);
    if(allOver65&&hasOpt3) errors.push('All drivers are 65+ — PIP must be Opt.6 (Medicare), not Opt.3');
    if(anyUnder65&&hasOpt6) errors.push('Driver(s) under 65 on policy — PIP must be Opt.3, not Opt.6');
  }

  // ── Bundle Detection & Home Checks ──
  const isBundle=/Farmers\s+[Hh]ome\s+quote/i.test(t)||/Farmers\s+Home\s+quote\s+summary/i.test(t);
  let homeData={isBundle,ppValOk:true,roofValOk:true,dwelling:null,homePay1:null};
  if(isBundle){
    // Personal Property Valuation
    homeData.ppValOk=/Personal\s+property\s+valuation\s+Replacement\s+cost/i.test(t);
    if(!homeData.ppValOk) errors.push('Home: Personal Property Valuation must be Replacement Cost');
    // Roof Valuation
    homeData.roofValOk=/Roof\s+valuation\s+Replacement\s+cost/i.test(t);
    if(!homeData.roofValOk) errors.push('Home: Roof Valuation must be Replacement Cost');

    // Dwelling amount (Coverage A)
    const dwellingMatch=t.match(/Dwelling\s+\$([\d,]+)/i);
    homeData.dwelling=dwellingMatch?parseInt(dwellingMatch[1].replace(/,/g,'')):null;
    // Home annual amount WITHOUT fees. PDF.js can split four-digit amounts like
    // "$1,023.00" into "$1, 023.00", so normalize those item-boundary spaces first.
    // Start at Home quote details to prevent the Auto payment plan being selected.
    const normalizedMoneyText=t
      .replace(/(\d),\s+(?=\d{3}(?:\D|$))/g,'$1,')
      .replace(/\$\s+(?=\d)/g,'$');
    const homeDetailsIdx=normalizedMoneyText.search(/Home\s+quote\s+details/i);
    const homeChunk=homeDetailsIdx>-1
      ? normalizedMoneyText.substring(homeDetailsIdx,homeDetailsIdx+14000)
      : normalizedMoneyText;

    // "Term Premium" is already the annual premium excluding fees (preferred).
    const termPremiumMatch=homeChunk.match(
      /Term\s+Premium\s+\$([\d,]+(?:\.\d{1,2})?)(?:\s*\/\s*12\s*-?\s*mo)?/i
    );
    let rawHomePay=termPremiumMatch
      ? parseFloat(termPremiumMatch[1].replace(/,/g,''))
      : null;

    // Fallback for layouts that only show the 1 Pay total including fees.
    if(rawHomePay===null){
      const home1PayMatch=homeChunk.match(
        /1\s+Pay\s+\$([\d,]+(?:\.\d{1,2})?)\s+-?\s*\$([\d,]+(?:\.\d{1,2})?)/i
      ) || homeChunk.match(/1\s+Pay\s+\$([\d,]+(?:\.\d{1,2})?)/i);
      if(home1PayMatch){
        rawHomePay=parseFloat((home1PayMatch[2]||home1PayMatch[1]).replace(/,/g,''));
        const homeFeeMatch=homeChunk.match(
          /\*?Includes\s+\$([\d,]+(?:\.\d{1,2})?)\s+(?:in\s+fees|Membership\s+fee)/i
        );
        const homeFee=homeFeeMatch
          ? parseFloat(homeFeeMatch[1].replace(/,/g,''))
          : 0;
        rawHomePay-=homeFee;
      }
    }
    homeData.homePay1=rawHomePay;
  }

  // ── AgencyZoom Checklist Data ──
  // Vehicles: Year Make Model (first word only), comma separated
  const azVehicles=vehicles.map(v=>{
    // Model from PDF is like "Chevrolet Blazer ..." or "Lincoln Nautilus ..."
    // We want: Year Make FirstModelWord e.g. "2026 Chevrolet Blazer"
    const rawWords=v.model.trim().split(/\s+/).filter(w=>w&&!/^\.+$/.test(w)&&w!=='...');
    // Take first 2 words (Make + Model) only
    const cleanParts=rawWords.slice(0,2).map(w=>w.replace(/\.+$/,'').replace(/\.\.\.$/,''));
    return v.year+' '+cleanParts.join(' ');
  }).join(', ');

  // Monthly Auto — round
  const azMonthly=monthlyEFT?Math.round(monthlyEFT):null;

  // Auto_6months = 1 Pay amount WITHOUT fees
  // IMPORTANT: use the complete Auto section (already isolated above as autoChunkEarly)
  // so the Membership fee line is not missed on longer quotes.
  let az6months=null;
  const autoChunk=autoChunkEarly || '';

  // Patterns:
  // Farmers:     1 Pay $1,981.00 - $1,981.00
  // Bristol West:1 Pay $1,105.24 $0.00 $1,105.24
  const onePay1=autoChunk.match(/1\s+Pay\s+\$([\d,]+\.?\d*)\s+\$0\.00\s+\$([\d,]+\.?\d*)/i);
  const onePay2=autoChunk.match(/1\s+Pay\s+\$([\d,]+\.?\d*)\s+-\s+\$([\d,]+\.?\d*)/i);
  const onePay3=autoChunk.match(/1\s+Pay\s+\$([\d,]+\.?\d*)/i);

  let rawAuto6=null;
  if(onePay1) rawAuto6=parseFloat(onePay1[2].replace(/,/g,''));
  else if(onePay2) rawAuto6=parseFloat(onePay2[2].replace(/,/g,''));
  else if(onePay3) rawAuto6=parseFloat(onePay3[1].replace(/,/g,''));

  // Subtract Auto fees from 1 Pay:
  // e.g. $1,981 - $60 Membership fee = $1,921
  if(rawAuto6!==null){
    const autoFeeMatch=autoChunk.match(
      /\*?Includes\s+\$([\d,]+(?:\.\d{1,2})?)\s+(?:in\s+fees|Membership\s+fee)/i
    );
    const autoFee=autoFeeMatch
      ? parseFloat(autoFeeMatch[1].replace(/,/g,''))
      : 0;

    az6months=Math.round(rawAuto6-autoFee);
  }

  // Home Annual — round
  const azHomeAnnual=homeData.homePay1?Math.round(homeData.homePay1):null;

  // Home Coverage A always uses the extracted Dwelling amount.
  const azCoverageA=homeData.dwelling;

  const azChecklist={vehicles:azVehicles,monthly:azMonthly,sixMonths:az6months,
    homeAnnual:azHomeAnnual,coverageA:azCoverageA,eftAutoMissing:eftAutoMissing};

  const status=errors.length>0?'fail':warnings.length>0?'warn':'pass';
  return{filename,name,quoteType,monthlyEFT,vehicleCount,vehicles,drivers,
    errors,warnings,status,putInStop,homeData,azChecklist,
    checks:{dateOk,dateDiff,prepDateStr,startDateStr,premiumOk}};
}

function parseDate(str){
  const m1=str.match(/(\d{2})\/(\d{2})\/(\d{2,4})/);
  if(m1){let y=parseInt(m1[3]);if(y<100)y+=2000;return new Date(y,parseInt(m1[1])-1,parseInt(m1[2]));}
  const m2=str.match(/([A-Za-z]+)\s+(\d+),?\s*(\d{4})/);
  if(m2)return new Date(`${m2[1]} ${m2[2]}, ${m2[3]}`);
  return null;
}

// ── EXTRACT VEHICLES ──
// Reads from summary page "Coverage for your property" table
// Two layouts exist:
// A) Interleaved: VehName, val1, val2, VehName, val1, val2 (values right after each name)
// B) Grouped: VehName, VehName, VehName, val1, val2, val1, val2 (all names then all values)
function extractVehicles(t){
  const vehicles=[];
  const lines=t.split('\n').map(l=>l.trim()).filter(Boolean);
  const isVal=v=>/^\$[\d,]+$/.test(v)||/^-$/.test(v)||/^included$/i.test(v)||/^none$/i.test(v);
  const toNum=v=>{
    if(!v||/^[-]$/.test(v)||/^(included|none)$/i.test(v)) return null;
    return v.replace(/[$,]/g,'');
  };
  const isVehLine=l=>/^((?:19|20)\d{2})\s+[A-Za-z]/.test(l);

  // ── STEP 1: Find "Coverage for your property" table on summary page ──
  let tableStart=-1;
  for(let i=0;i<lines.length;i++){
    if(/Coverage for your property/i.test(lines[i])){
      for(let j=i+1;j<Math.min(i+20,lines.length);j++){
        if(isVehLine(lines[j])){tableStart=i;break;}
      }
      if(tableStart>-1) break;
    }
  }
  if(tableStart===-1) return vehicles;

  // ── STEP 2: Detect which columns exist in the summary table header ──
  // The header line(s) between "Coverage for your property" and the first vehicle
  // tell us whether Comprehensive and/or Collision columns are present.
  // e.g. "Vehicle Comprehensive Collision" → both
  //      "Vehicle Collision"               → collision only (no comp)
  //      "Vehicle"                         → PLPD only (no values expected)
  let hasCompCol=false, hasCollCol=false;
  for(let i=tableStart;i<Math.min(tableStart+10,lines.length);i++){
    const l=lines[i];
    if(/Comprehensive/i.test(l)) hasCompCol=true;
    if(/Collision/i.test(l)) hasCollCol=true;
    if(isVehLine(l)) break; // stop at first vehicle line
  }
  // If neither header found, default to both (older layout)
  if(!hasCompCol&&!hasCollCol){ hasCompCol=true; hasCollCol=true; }

  // ── STEP 3: Collect entries (skip noise) ──
  const entries=[];
  for(let i=tableStart+1;i<lines.length&&i<tableStart+100;i++){
    const line=lines[i];
    if(/Additional selected|Discounts \/|Page \d+\s+of/i.test(line)) break;
    if(/^Vehicle$|^Comprehensive$|^Collision$|^Standard$|Aaron Budnick|License|farmersagent|\(517\)/i.test(line)) continue;
    if(/^Comp(rehensive)?\s+Coll(ision)?$/i.test(line)) continue;
    entries.push(line);
  }

  // ── STEP 4: Parse vehicle names + values using column knowledge ──
  const tempVehicles=[];
  let i=0;
  while(i<entries.length){
    const line=entries[i];
    const m=line.match(/^((?:19|20)\d{2})\s+([A-Za-z0-9][a-zA-Z0-9 \-\/\.]{2,50}?)(?=\s+[\$\(]|\s{2,}|$)/) || line.match(/^((?:19|20)\d{2})\s+([A-Za-z0-9][a-zA-Z0-9 \-\/\.]{2,50})/);
    if(m){
      const year=parseInt(m[1]);
      const model=m[2].trimEnd(); // trimEnd to remove trailing spaces before value
      let compVal=null, collVal=null;

      // Extract whatever comes AFTER the model name on the same line
      // e.g. "2011 Chevrolet Camaro ...   $1,000" → afterModel = "$1,000"
      // Extract afterModel — strip truncated suffix like (Ne... or & C... before values
      let afterModel=line.slice(m[0].length).trim();
      // If afterModel starts with ( or & (truncated name suffix), skip to first $
      if(afterModel.startsWith('(')||afterModel.startsWith('&')){
        const dollarIdx=afterModel.indexOf('$');
        if(dollarIdx>-1) afterModel=afterModel.slice(dollarIdx).trim();
        else afterModel='';
      }

      // Normalize afterModel — remove "Standard/Limited/Broadened" attached to values
      // e.g. "$1,000Standard" → "$1,000" and "$1,000 $1,000Standard" → "$1,000 $1,000"
      const normAfter = afterModel.replace(/(\$[\d,]+|-)(Standard|Limited|Broadened)/gi,'$1 $2').trim();
      // Check for two inline values: "$1,000 $1,000"
      const inlineTwo=normAfter.match(/^(\$[\d,]+|-)\s+(\$[\d,]+|-)(?:\s+(?:Standard|Limited|Broadened))?\s*$/i);
      // Check for one inline value: "$1,000" or "$1,000 Standard"
      const inlineOne=normAfter.match(/^(\$[\d,]+|-)(?:\s+(?:Standard|Limited|Broadened))?\s*$/i);

      if(inlineTwo&&hasCompCol&&hasCollCol){
        compVal=toNum(inlineTwo[1]);
        collVal=toNum(inlineTwo[2]);
      } else if(inlineTwo&&!hasCompCol&&hasCollCol){
        collVal=toNum(inlineTwo[1]);
      } else if(inlineTwo&&hasCompCol&&!hasCollCol){
        compVal=toNum(inlineTwo[1]);
      } else if(inlineOne){
        // Single value inline — assign to whichever column exists
        if(!hasCompCol&&hasCollCol) collVal=toNum(inlineOne[1]);
        else if(hasCompCol&&!hasCollCol) compVal=toNum(inlineOne[1]);
        else compVal=toNum(inlineOne[1]); // fallback: treat as comp
      } else {
        // Values on next lines — skip "Standard/Limited" noise lines
        const nextLines=[];
        for(let k=i+1;k<Math.min(i+5,entries.length);k++){
          const nl=entries[k];
          if(/^(Standard|Limited|Broadened|Michigan)/i.test(nl)) continue;
          if(isVal(nl)) nextLines.push(nl);
          if(nextLines.length===2) break;
        }
        if(hasCompCol&&hasCollCol){
          if(nextLines[0]!==undefined) compVal=toNum(nextLines[0]);
          if(nextLines[1]!==undefined) collVal=toNum(nextLines[1]);
        } else if(!hasCompCol&&hasCollCol){
          if(nextLines[0]!==undefined) collVal=toNum(nextLines[0]);
        } else if(hasCompCol&&!hasCollCol){
          if(nextLines[0]!==undefined) compVal=toNum(nextLines[0]);
        }
      }
      tempVehicles.push({year,model,compVal,collVal,_entryIndex:i});
    }
    i++;
  }

  // ── STEP 5: Grouped layout fallback ──
  // If all vehicles still have null values, try grouped layout
  const allNull=tempVehicles.length>0&&tempVehicles.every(v=>v.compVal===null&&v.collVal===null);
  if(allNull){
    const valBlock=entries.filter(e=>isVal(e));
    const colCount=(hasCompCol?1:0)+(hasCollCol?1:0)||2;
    for(let j=0;j<tempVehicles.length;j++){
      let vi=0;
      if(hasCompCol){ tempVehicles[j].compVal=valBlock[j*colCount+vi]!==undefined?toNum(valBlock[j*colCount+vi]):null; vi++; }
      if(hasCollCol){ tempVehicles[j].collVal=valBlock[j*colCount+vi]!==undefined?toNum(valBlock[j*colCount+vi]):null; }
    }
  }

    for(const v of tempVehicles){
    delete v._entryIndex;
    vehicles.push(v);
  }
  return vehicles;
}

// ── EXTRACT DRIVERS ──
function extractDrivers(t){
  const drivers=[];
  const seen=new Set();
  const re=/\b([A-Z][a-z]+(?:\s+[A-Z][a-z]+)+),\s*(\d{2})\b/g;
  let m;
  while((m=re.exec(t))!==null){
    const name=m[1].trim();
    const age=parseInt(m[2]);
    const key=name+'|'+age;
    if(age>=16&&age<=100&&!seen.has(key)&&!/^(Aaron|Budnick|Agency|Farmers|Bristol)/i.test(name)&&name.includes(' ')){
      seen.add(key);drivers.push({name,age});
    }
  }
  return drivers;
}

// ── COMMON LIABILITY & PIP CHECKS ──
function checkLiabilityPIP(t,errors){
  if(!/Bodily\s+injury\s+\$100,000\/\$300,000/i.test(t)) errors.push('Bodily Injury must be $100,000/$300,000');
  if(!/Property\s+damage\s+\$100,000(?!\s*\/)/i.test(t)) errors.push('Property Damage must be $100,000');
  if(!/(?:UM\/UIM|Uninsured\s+motorist|Underinsured\s+motorist)[^\n$]*\$100,000\/\$300,000/i.test(t)) errors.push('UM/UIM must be $100,000/$300,000');
  const pipLine=t.match(/Personal\s+(?:Injury\s+)?[Pp]rotection\s+[Mm]edical\s+(Opt\.[\s\S]{0,80}?)(?=PIP\s+medical|PIP\s+wage|Work\s+loss)/i);
  const pipStr=pipLine?pipLine[1].trim():'';
  const isOpt3=/Opt\.\s*3/i.test(pipStr);
  const isOpt6=/Opt\.\s*6/i.test(pipStr);
  if(!isOpt3&&!isOpt6) errors.push(`PIP must be Opt.3 or Opt.6 (found: ${pipStr.substring(0,30)||'not found'})`);
  if(isOpt3){
    if(!/\$250,000[\s\S]{0,10}\/\$500/i.test(t)&&!/no\s+exclusions[\s\S]{0,10}\/\$500/i.test(t))
      errors.push('PIP deductible must be $500 for Opt.3');
  }
  if(isOpt6&&!/\/\$0/i.test(t)) errors.push('PIP deductible must be $0 for Opt.6');
  if(!/PIP\s+medical\s+Primary/i.test(t)) errors.push('PIP Medical must be Primary');
  if(!/PIP\s+wage\s+loss\s+Primary/i.test(t)) errors.push('PIP Wage Loss must be Primary');
}

// ── TYPE 1: FARMERS ──
function checkFarmers(t,errors,warnings,vehicles){
  checkLiabilityPIP(t,errors);
  if(/Signal\s+by\s+Farmers/i.test(t)) errors.push('Signal by Farmers must be REMOVED');
  if(!/Auto\/Home\s+or\s+Condo/i.test(t)) errors.push('Farmers: Auto/Home or Condo discount is MISSING');
  checkVehicleCoverage(t,vehicles,errors);
}

// ── TYPE 2: FARMER-BRISTOL ──
function checkFarmerBristol(t,errors,warnings,vehicles){
  checkLiabilityPIP(t,errors);
  if(/Signal\s+by\s+Farmers/i.test(t)) errors.push('Signal by Farmers must be REMOVED');
  if(!/Auto\/Farmers\s+Home/i.test(t)) errors.push('Farmer-Bristol: Auto/Farmers Home discount is MISSING');
  checkVehicleCoverage(t,vehicles,errors);
}

// ── TYPE 3: PURE BRISTOL WEST ──
function checkPureBristol(t,errors,warnings,vehicles){
  checkLiabilityPIP(t,errors);
  // Extract discount section from detail page
  const discMatch=t.match(/Discounts\/Preferences\s+([\s\S]{0,400}?)(?:Included|Payment\s+plans)/i);
  const discText=discMatch?discMatch[1]:'';
  // Paperless must be present
  if(!/Go\s+Paperless|Paperless/i.test(discText)) errors.push('Bristol West: Paperless discount is MISSING');
  // Only flag manually added discounts (not defaults: EFT, Safe Driver, Preferred Driver)
  const forbidden=[
    {name:'Signal by Farmers',rx:/Signal\s+by\s+Farmers/i},
    {name:'Homeowner',rx:/\bHomeowner\b/i},
    {name:'Auto\/Farmers Home',rx:/Auto\/Farmers\s+Home/i},
    {name:'Auto\/Home or Condo',rx:/Auto\/Home\s+or\s+Condo/i},
  ];
  for(const f of forbidden){
    if(f.rx.test(discText)) errors.push(`Bristol West: "${f.name}" should NOT be checked`);
  }
  checkVehicleCoverage(t,vehicles,errors);
}

// ── VEHICLE COVERAGE CHECK ──
// Uses summary page values (compVal/collVal) extracted with vehicle
function checkVehicleCoverage(t,vehicles,errors){
  const tUp=t.toUpperCase();

  // Locate the most likely detailed coverage heading for each vehicle. A quote can
  // mention the same vehicle in summaries and detail pages, so prefer the mention
  // followed by coverage labels instead of taking the first match in the PDF.
  const vehicleStarts=vehicles.map(v=>{
    const words=v.model.split(/\s+/).filter(Boolean);
    const keys=[
      `${v.year} ${words.slice(0,3).join(' ')}`.toUpperCase(),
      `${v.year} ${words[0]||''}`.toUpperCase()
    ].filter((key,index,array)=>key.trim()&&array.indexOf(key)===index);
    let bestIndex=-1;
    let bestScore=-1;
    for(const key of keys){
      let from=0;
      while(from<tUp.length){
        const found=tUp.indexOf(key,from);
        if(found===-1) break;
        const immediate=t.substring(found,found+220);
        const preview=t.substring(found,found+600);
        const score=(/\bCoverage\b/i.test(immediate)?20:0)+[
          /\bCoverage\b/i,
          /Liability\s+and\s+policy\s+coverages/i,
          /Comprehensive/i,
          /Collision/i,
          /Roadside\s+assistance/i,
          /MCCA\s+assessment/i,
          /Vehicle\s+premium/i
        ].reduce((total,rx)=>total+(rx.test(preview)?1:0),0);
        // Prefer the later occurrence when scores tie; detailed pages normally
        // follow summary pages in the extracted PDF text.
        if(score>=bestScore){bestScore=score;bestIndex=found;}
        from=found+key.length;
      }
    }
    return {vehicle:v,start:bestIndex};
  });

  const orderedStarts=vehicleStarts
    .filter(item=>item.start>=0)
    .map(item=>item.start)
    .sort((a,b)=>a-b);

  for(let i=0;i<vehicles.length;i++){
    const v=vehicles[i];
    const yr=v.year;
    const short=`${yr} ${v.model.split(' ').slice(0,3).join(' ')}`;

    // Normalize compVal/collVal to number or null for reliable comparison
    const normalizeVal=val=>{
      if(val===null||val===undefined) return null;
      const n=parseInt(String(val).replace(/[$,]/g,''),10);
      return isNaN(n)?null:n;
    };
    const compNum=normalizeVal(v.compVal);
    const collNum=normalizeVal(v.collVal);
    const hasComp=compNum!==null;
    const hasColl=collNum!==null;
    const compIs1000=compNum===1000;
    const collIs1000=collNum===1000;

    // Roadside must appear inside this vehicle's own coverage section. Stop at
    // the next vehicle heading so another vehicle's roadside cannot cause a pass.
    const start=vehicleStarts[i].start;
    const nextStart=orderedStarts.find(pos=>pos>start);
    const end=nextStart===undefined?t.length:nextStart;
    let vehSection=start>=0?t.substring(start,end):'';
    const premiumIndex=vehSection.search(/Vehicle\s+premium/i);
    if(premiumIndex>=0){
      const premiumLineEnd=vehSection.indexOf('\n',premiumIndex);
      if(premiumLineEnd>=0) vehSection=vehSection.substring(0,premiumLineEnd);
    }
    const hasRoadside=/Roadside\s+assistance/i.test(vehSection);

    if(yr>=2015){
      if(!hasComp) errors.push(`${short}: Missing Comprehensive (2015+ = Full Coverage)`);
      else if(!compIs1000) errors.push(`${short}: Comprehensive deductible must be $1,000 (found $${compNum})`);
      if(!hasColl) errors.push(`${short}: Missing Collision (2015+ = Full Coverage)`);
      else if(!collIs1000) errors.push(`${short}: Collision deductible must be $1,000 (found $${collNum})`);
    } else {
      if(hasComp) errors.push(`${short}: Has Comprehensive — pre-2015 must be PLPD only`);
      if(hasColl) errors.push(`${short}: Has Collision — pre-2015 must be PLPD only`);
    }
    if(!hasRoadside) errors.push(`${short}: Roadside Assistance is missing`);
  }
}

// ── RENDER TABLE ──
function renderTable(){
  const tbody=document.getElementById('resultsBody');
  const search=document.getElementById('searchBox').value.toLowerCase();
  const filtered=allResults.filter(r=>{
    const mf=activeFilter==='all'||r.status===activeFilter;
    const ms=!search||r.name.toLowerCase().includes(search)||r.filename.toLowerCase().includes(search);
    return mf&&ms;
  });
  tbody.innerHTML='';
  if(!filtered.length){
    tbody.innerHTML=`<tr><td colspan="7" style="padding:40px;text-align:center;color:var(--muted);font-size:13px;">No results match.</td></tr>`;
    return;
  }
  filtered.forEach((r,idx)=>{
    const uid='r_'+idx+'_'+Math.random().toString(36).substr(2,5);
    const tr=document.createElement('tr');
    tr.className='row-card';
    const statusBadge=r.status==='pass'?`<span class="status-badge status-pass">✓ PASS</span>`:
      r.status==='warn'?`<span class="status-badge status-warn">⚠ REVIEW</span>`:
      `<span class="status-badge status-fail">✗ FLAGGED</span>`;
    const typeChip=r.quoteType==='Farmers'?`<span class="chip chip-farmers">Farmers</span>`:
      r.quoteType==='Farmer-Bristol'?`<span class="chip chip-farmer-bristol">Farmer→Bristol</span>`:
      r.quoteType==='Bristol West'?`<span class="chip chip-bristol">Bristol West</span>`:
      `<span class="chip chip-unknown">Unknown</span>`;
    const pills=[
      ...r.errors.map(e=>`<span class="err-pill">⚑ ${e}</span>`),
      ...r.warnings.map(w=>`<span class="warn-pill">⚠ ${w}</span>`)
    ].join('')||`<span class="ok-text">✓ All checks passed</span>`;
    const eft=r.monthlyEFT?`$${r.monthlyEFT.toFixed(2)}/mo`:'—';
    const stopBanner=r.putInStop?`<div class="stop-banner">⛔ PUT IN STOP — Premium exceeds threshold</div>`:'';
    tr.innerHTML=`<td colspan="7">
      ${stopBanner}
      <div class="row-main" onclick="toggleDetail('${uid}')">
        <span class="row-expand" id="exp_${uid}">▶</span>
        <div class="col-name"><div class="cname">${r.name}</div><div class="fname">${r.filename}</div></div>
        <div>${typeChip}</div>
        <div style="font-size:13px;color:var(--text2);">${r.vehicleCount} vehicle${r.vehicleCount!==1?'s':''}</div>
        <div style="font-size:13px;">${eft}</div>
        <div>${statusBadge}</div>
        <div>${pills}</div>
        <div><button class="debug-btn" onclick="event.stopPropagation();openDebug(this)" title="Show raw parse debug">🔍 Debug</button></div>
      </div>
      <div class="row-details" id="${uid}">${buildDetails(r)}</div>
    </td>`;
    tr.dataset.filename=r.filename;
    tbody.appendChild(tr);
  });
}

function toggleDetail(uid){
  document.getElementById(uid).classList.toggle('open');
  document.getElementById('exp_'+uid).classList.toggle('open');
}

function buildDetails(r){
  const c=r.checks||{};
  const ck=(ok,label,val='',isWarn=false)=>{
    const icon=ok?'✓':isWarn?'⚠':'✗';
    const cls=ok?'ci-ok':isWarn?'ci-warn':'ci-fail';
    const vc=ok?'ok':isWarn?'neutral':'bad';
    return `<div class="check-item"><span class="ci-icon ${cls}">${icon}</span>
      <span class="ci-label">${label}</span>
      ${val?`<span class="ci-val ${vc}">${val}</span>`:''}</div>`;
  };
  // Vehicles
  let vHTML='';
  for(const v of r.vehicles){
    const isNew=v.year>=2015;
    // Use both year AND partial model name to match errors correctly (avoids same-year collision)
    const shortModel=v.model.split(' ').slice(0,3).join(' ');
    const vKey=`${v.year} ${shortModel}`.toLowerCase();
    const cErr=r.errors.some(e=>e.toLowerCase().includes(vKey)&&e.toLowerCase().includes('comprehensive'));
    const colErr=r.errors.some(e=>e.toLowerCase().includes(vKey)&&e.toLowerCase().includes('collision'));
    const rErr=r.errors.some(e=>e.toLowerCase().includes(vKey)&&e.toLowerCase().includes('roadside'));
    vHTML+=`<div class="vehicle-item">
      <div class="v-name">${v.year} ${v.model.split(' ').slice(0,4).join(' ')}</div>
      <div class="v-tags"><span class="${isNew?'v-ok':'v-info'}">${isNew?'Full Coverage':'PLPD'}</span></div>
      <div style="margin-top:5px;display:flex;flex-wrap:wrap;gap:4px;">
        <span class="${cErr?'v-fail':'v-ok'}">${isNew?(cErr?'✗ Comp missing':'✓ Comp $1,000'):'✓ No Comp'}</span>
        <span class="${colErr?'v-fail':'v-ok'}">${isNew?(colErr?'✗ Collision missing':'✓ Collision $1,000'):'✓ No Collision'}</span>
        <span class="${rErr?'v-fail':'v-ok'}">${rErr?'✗ No Roadside':'✓ Roadside'}</span>
      </div>
    </div>`;
  }
  if(!vHTML) vHTML=`<div style="font-size:12px;color:var(--muted);">No vehicles detected</div>`;
  // Drivers
  let dHTML='';
  for(const d of r.drivers){
    const is65=d.age>=65;
    dHTML+=`<div class="check-item">
      <span class="ci-icon ${is65?'ci-warn':'ci-ok'}">${is65?'⚠':'✓'}</span>
      <span class="ci-label">${d.name}</span>
      <span class="ci-val ${is65?'bad':'ok'}">Age ${d.age}${is65?' (65+)':''}</span>
    </div>`;
  }
  if(!dHTML) dHTML=`<div style="font-size:12px;color:var(--muted);">No drivers detected</div>`;
  // Discount label
  const discLabel=r.quoteType==='Farmers'?'Auto/Home or Condo required':
    r.quoteType==='Farmer-Bristol'?'Auto/Farmers Home required':
    'Paperless only (defaults allowed)';
  const discOk=r.quoteType==='Farmers'?!r.errors.some(e=>/Auto\/Home or Condo/i.test(e)):
    r.quoteType==='Farmer-Bristol'?!r.errors.some(e=>/Auto\/Farmers Home/i.test(e)):
    !r.errors.some(e=>/Paperless|should NOT/i.test(e));
  // Premium — limits apply only to 1–3 vehicles
  const limits={1:400,2:800,3:1000};
  let premRow;
  if(r.monthlyEFT){
    if(r.vehicleCount>=1 && r.vehicleCount<=3){
      const limit=limits[r.vehicleCount];
      premRow=ck(c.premiumOk,`Monthly EFT vs $${limit} (${r.vehicleCount} vehicle${r.vehicleCount!==1?'s':''})`,`$${r.monthlyEFT.toFixed(2)}/mo`);
    } else {
      premRow=ck(true,`Monthly EFT — No limit for ${r.vehicleCount} vehicles`,`$${r.monthlyEFT.toFixed(2)}/mo`);
    }
  } else {
    premRow=`<div class="check-item"><span class="ci-icon ci-fail">✗</span><span class="ci-label">Monthly EFT auto not available — use BW</span></div>`;
  }

  return `<div class="row-details-grid">
    <div class="detail-group">
      <h4>Liability</h4>
      ${ck(!r.errors.some(e=>/Bodily Injury/i.test(e)),'Bodily Injury','$100K/$300K')}
      ${ck(!r.errors.some(e=>/Property Damage/i.test(e)),'Property Damage','$100,000')}
      ${ck(!r.errors.some(e=>/UM\/UIM/i.test(e)),'UM/UIM','$100K/$300K')}
    </div>
    <div class="detail-group">
      <h4>PIP</h4>
      ${ck(!r.errors.some(e=>/PIP must be/i.test(e)),'PIP Option','Opt.3 or Opt.6')}
      ${ck(!r.errors.some(e=>/PIP deductible/i.test(e)),'PIP Deductible','Opt.3→$500 / Opt.6→$0')}
      ${ck(!r.errors.some(e=>/PIP Medical/i.test(e)),'PIP Medical','Primary')}
      ${ck(!r.errors.some(e=>/PIP Wage/i.test(e)),'PIP Wage Loss','Primary')}
    </div>
    <div class="detail-group">
      <h4>Discounts (${r.quoteType})</h4>
      ${ck(discOk,discLabel)}
      ${r.quoteType==='Farmers'?ck(!r.errors.some(e=>/Signal/i.test(e)),'Signal discount removed'):''}
    </div>
    <div class="detail-group">
      <h4>Vehicles (${r.vehicles.length})</h4>
      ${vHTML}
    </div>
    <div class="detail-group">
      <h4>Drivers (${r.drivers.length})</h4>
      ${dHTML}
    </div>
    <div class="detail-group">
      <h4>Date &amp; Premium</h4>
      ${ck(c.dateOk,'Policy start date (14 days)',c.dateDiff!==null?`${c.dateDiff} days`:'')}
      ${premRow}
    </div>
  </div>
  ${r.homeData&&r.homeData.isBundle?buildHomePanel(r):''}
  ${buildAZPanel(r)}`;
}

function buildHomePanel(r){
  const h=r.homeData;
  return '<div class="row-details-grid" style="margin-top:0">'
    +'<div class="detail-group home-group">'
    +'<h4>🏠 Home QC</h4>'
    +(h.ppValOk
      ?'<div class="check-item"><span class="ci-icon ci-ok">✓</span><span class="ci-label">Personal Property Valuation</span><span class="ci-val ok">Replacement Cost</span></div>'
      :'<div class="check-item"><span class="ci-icon ci-fail">✗</span><span class="ci-label">Personal Property Valuation</span><span class="ci-val bad">NOT Replacement Cost</span></div>')
    +(h.roofValOk
      ?'<div class="check-item"><span class="ci-icon ci-ok">✓</span><span class="ci-label">Roof Valuation</span><span class="ci-val ok">Replacement Cost</span></div>'
      :'<div class="check-item"><span class="ci-icon ci-fail">✗</span><span class="ci-label">Roof Valuation</span><span class="ci-val bad">NOT Replacement Cost</span></div>')
    +(h.dwelling?'<div class="check-item"><span class="ci-icon ci-ok">✓</span><span class="ci-label">Dwelling (Cov A)</span><span class="ci-val neutral">$'+h.dwelling.toLocaleString()+'</span></div>':'')
    +'</div></div>';
}

function buildAZPanel(r){
  const az=r.azChecklist;
  if(!az) return "";
  function azRow(label,val,id){
    if(!val&&val!==0) return "";
    const dispVal=String(val);
    return "<div class='az-row'>"
      +"<span class='az-label'>"+label+"</span>"
      +"<span class='az-val' id='azv_"+id+"_"+r.filename.replace(/[^a-z0-9]/gi,'')+"'>"+dispVal+"</span>"
      +"<button class='az-copy' onclick='copyAZ(this)'>📋 Copy</button>"
      +"</div>";
  }
  let html="<div class='az-panel'>"
    +"<h4>📋 AgencyZoom Main Page</h4>"
    +(az.vehicles?azRow("Vehicles on Policy",az.vehicles,"veh"):"<div class='az-row'><span class='az-label'>Vehicles on Policy</span><span class='az-manual'>Not detected</span></div>")
    +(az.monthly?azRow("Monthly Auto","$"+az.monthly,"meft"):"<div class='az-row'><span class='az-label'>Monthly Auto</span><span class='ci-fail'>✗ Monthly EFT auto not available — use BW</span></div>")
    +(az.sixMonths?azRow("6 Months Auto","$"+az.sixMonths,"6mo"):"");
  if(r.homeData&&r.homeData.isBundle){
    html+=(az.homeAnnual?azRow("Home Annual Price","$"+az.homeAnnual,"hann"):"")
      +(az.coverageA?azRow("Home Coverage A","$"+az.coverageA.toLocaleString(),"hcova"):"");
  }
  if(r.quoteType==='Farmers'||r.quoteType==='Farmer-Bristol'){
    html+="<div class='az-row'><span class='az-label'>Star 1-3</span><span class='az-manual'>⚠ Fill manually</span></div>";
  }
  html+="</div>";
  return html;
}



function buildAZData(r){
  const v=r.vehicles||[];
  const az=r.azChecklist||{};
  const autoFields={};
  for(let i=0;i<5;i++){
    const veh=v[i];
    if(veh){
      const rawW=veh.model.trim().split(' ').filter(function(w){return w&&w!=='...'&&w!=='.';});
      const cleanW=rawW.slice(0,2).map(function(w){return w.replace(/\.+$/,'');});
      autoFields['auto'+(i+1)]=veh.year+' '+cleanW.join(' ');
      autoFields['auto'+(i+1)+'_ded']=veh.year>=2015?'$1000':'PLPD';
    } else {
      autoFields['auto'+(i+1)]='';
      autoFields['auto'+(i+1)+'_ded']='';
    }
  }
  return {
    vehicles_policy:az.vehicles||'',
    monthly_auto:az.monthly?String(az.monthly):'',
    auto_6months:az.sixMonths?String(az.sixMonths):'',
    bodily_injury:'$100,000/$300,000',
    home_coverage_a:az.coverageA?String(az.coverageA):'',
    home_annual:az.homeAnnual?String(az.homeAnnual):'',
    auto1:autoFields.auto1||'',
    auto1_ded:autoFields.auto1_ded||'',
    auto2:autoFields.auto2||'',
    auto2_ded:autoFields.auto2_ded||'',
    auto3:autoFields.auto3||'',
    auto3_ded:autoFields.auto3_ded||'',
    auto4:autoFields.auto4||'',
    auto4_ded:autoFields.auto4_ded||'',
    auto5:autoFields.auto5||'',
    auto5_ded:autoFields.auto5_ded||'',
    _name:r.name,
    _filename:r.filename,
    _isBundle:!!(r.homeData && r.homeData.isBundle),
    _ts:Date.now()
  };
}

function saveToLocalStorage(r){
  try{
    const data=buildAZData(r);
    localStorage.setItem('tritox_az_data',JSON.stringify(data));
    console.log('[TritoX] Saved for:', r.name, data);
    // Write to meta tag for Tampermonkey to read
    try{
      let meta = document.getElementById('tritox-tm-bridge');
      if(!meta){
        meta = document.createElement('meta');
        meta.id = 'tritox-tm-bridge';
        meta.name = 'tritox-data';
        document.head.appendChild(meta);
      }
      meta.content = JSON.stringify(data);
      console.log('[TritoX] Written to TM bridge meta tag');
    } catch(e){}
    // Store reset flag in localStorage so TM on AZ knows new PDF processed
    try{
      localStorage.setItem('tritox_new_pdf', Date.now().toString());
    } catch(e){}
    // Show ready status
    const bmReady=document.getElementById('bmReady');
    if(bmReady){
      bmReady.style.display='block';
      bmReady.textContent='✅ '+r.name+' — data saved! Tampermonkey will pick it up. Open the correct AgencyZoom lead → click 🚀 Fill + Attach PDF.';
    }
    const bmSec=document.getElementById('bmSection');
    if(bmSec) bmSec.style.display='block';
    const bmName=document.getElementById('bmName');
    if(bmName) bmName.textContent=r.name;
  } catch(e){
    console.error('[TritoX] Save failed:',e);
  }
}

function openDebug(btn){
  const tr=btn.closest('tr');
  const fn=tr?tr.dataset.filename:'';
  const r=allResults.find(x=>x.filename===fn);
  if(r) showDebug(r);
}
function showDebug(r){
  const v=r.vehicles;
  const raw=r._rawText||'(not stored)';
  const idx=raw.toLowerCase().indexOf('coverage for your property');
  const tableSnippet=(idx>-1?raw.substring(idx,idx+600):'(not found)').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
  const detIdx=raw.toLowerCase().indexOf('vehicles\n');
  const detSnippet=(detIdx>-1?raw.substring(detIdx,detIdx+800):'(not found)').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');

  let vRows='';
  for(const veh of v){
    const cColor=veh.compVal?'#00e887':'#ff4d6a';
    const oColor=veh.collVal?'#00e887':'#ff4d6a';
    const cVal=veh.compVal!==null?'$'+veh.compVal:'null (no comp)';
    const oVal=veh.collVal!==null?'$'+veh.collVal:'null (no coll)';
    vRows+='<tr><td>'+veh.year+' '+veh.model+'</td>'
      +'<td style="color:'+cColor+'">'+cVal+'</td>'
      +'<td style="color:'+oColor+'">'+oVal+'</td></tr>';
  }

  const vTable=v.length
    ?('<table style="width:100%;border-collapse:collapse;font-size:13px;margin-bottom:20px;">'
      +'<tr style="color:#8a97bb;border-bottom:1px solid #252d45;">'
      +'<th style="text-align:left;padding:6px 8px;">Vehicle</th>'
      +'<th style="text-align:left;padding:6px 8px;">compVal</th>'
      +'<th style="text-align:left;padding:6px 8px;">collVal</th>'
      +'</tr>'+vRows+'</table>')
    :'<p style="color:#ff4d6a;font-size:13px;margin-bottom:20px;">⚠ No vehicles extracted!</p>';

  const modal=document.createElement('div');
  modal.style.cssText='position:fixed;inset:0;background:rgba(0,0,0,0.85);z-index:9999;display:flex;align-items:center;justify-content:center;padding:20px;';

  const box=document.createElement('div');
  box.style.cssText='background:#111520;border:1px solid #252d45;border-radius:16px;max-width:800px;width:100%;max-height:90vh;overflow-y:auto;padding:28px;';

  box.innerHTML='<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:20px;">'
    +'<h3 style="font-family:Orbitron,sans-serif;color:#00d4ff;font-size:16px;">🔍 Debug — '+r.name+'</h3>'
    +'<button id="dbgClose" style="background:#1e2438;border:1px solid #252d45;color:#dce4f5;border-radius:8px;padding:6px 14px;cursor:pointer;font-size:13px;">✕ Close</button>'
    +'</div>'
    +'<h4 style="color:#8a97bb;font-size:12px;text-transform:uppercase;letter-spacing:1px;margin-bottom:8px;">Parsed Vehicles</h4>'
    +vTable
    +'<h4 style="color:#8a97bb;font-size:12px;text-transform:uppercase;letter-spacing:1px;margin-bottom:8px;">Raw — Summary Table</h4>'
    +'<pre style="background:#080a0f;border:1px solid #252d45;border-radius:8px;padding:12px;font-size:11px;color:#dce4f5;white-space:pre-wrap;overflow-x:auto;margin-bottom:20px;max-height:200px;overflow-y:auto;">'+tableSnippet+'</pre>'
    +'<h4 style="color:#8a97bb;font-size:12px;text-transform:uppercase;letter-spacing:1px;margin-bottom:8px;">Raw — Vehicle Detail Section</h4>'
    +'<pre style="background:#080a0f;border:1px solid #252d45;border-radius:8px;padding:12px;font-size:11px;color:#dce4f5;white-space:pre-wrap;overflow-x:auto;max-height:200px;overflow-y:auto;">'+detSnippet+'</pre>';

  modal.appendChild(box);
  document.body.appendChild(modal);
  box.querySelector('#dbgClose').addEventListener('click',function(){modal.remove();});
  modal.addEventListener('click',function(e){if(e.target===modal)modal.remove();});
}


function copyAZ(btn){
  const row=btn.closest('.az-row');
  const valEl=row.querySelector('.az-val');
  if(!valEl) return;
  const val=valEl.textContent.trim();
  navigator.clipboard.writeText(val).then(function(){
    btn.textContent='✓ Copied';
    btn.classList.add('copied');
    setTimeout(function(){btn.textContent='📋 Copy';btn.classList.remove('copied');},2000);
  }).catch(function(){
    // Fallback for older browsers
    const ta=document.createElement('textarea');
    ta.value=val;ta.style.position='fixed';ta.style.opacity='0';
    document.body.appendChild(ta);ta.select();
    document.execCommand('copy');document.body.removeChild(ta);
    btn.textContent='✓ Copied';btn.classList.add('copied');
    setTimeout(function(){btn.textContent='📋 Copy';btn.classList.remove('copied');},2000);
  });
}


function generateBookmarklet(){
  const code = `(function(){
  var d=JSON.parse(localStorage.getItem('tritox_az_data')||'{}');
  if(!d.vehicles_policy&&!d.auto1){alert('No TritoX data found!\\nPlease process a PDF in TritoX first.');return;}
  var ok=0,fail=0;
  function fillText(id,val){
    if(val===undefined||val===null||val==='')return;
    var el=document.getElementById('customfields-'+id);
    if(!el){fail++;return;}
    try{
      var s=Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set;
      s.call(el,val);
      el.dispatchEvent(new Event('input',{bubbles:true}));
      el.dispatchEvent(new Event('change',{bubbles:true}));
      ok++;
    }catch(e){fail++;}
  }
  function fillSelect(id,val){
    if(val===undefined||val===null||val==='')return;
    var el=document.getElementById('customfields-'+id);
    if(!el){fail++;return;}
    try{
      var s=Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,'value').set;
      s.call(el,val);
      el.dispatchEvent(new Event('change',{bubbles:true}));
      ok++;
    }catch(e){fail++;}
  }
  fillText('cf30203',d.vehicles_policy);
  fillText('cf30197',d.monthly_auto);
  fillText('cf30199',d.auto_6months);
  fillSelect('cf56698',d.bodily_injury);
  fillText('cf47028',d.home_coverage_a);
  fillText('cf37981',d.home_annual);
  fillText('cf56654',d.auto1);
  fillSelect('cf56655',d.auto1_ded);
  fillText('cf56656',d.auto2);
  fillSelect('cf56657',d.auto2_ded);
  fillText('cf56692',d.auto3);
  fillSelect('cf56693',d.auto3_ded);
  fillText('cf56694',d.auto4);
  fillSelect('cf56695',d.auto4_ded);
  fillText('cf56696',d.auto5);
  fillSelect('cf56697',d.auto5_ded);
  if(fail===0){
    alert('\\u2705 All fields filled for: '+( d._name||'Unknown')+'\\nPlease review and click Save/Update.');
  } else {
    alert('\\u26a0 Filled '+ok+' fields. '+fail+' fields not found.\\nMake sure you are on the lead Main page in AgencyZoom.');
  }
})();`;
  return 'javascript:'+encodeURIComponent(code);
}

function initBookmarklet(){
  const bmLink = document.getElementById('bmLink');
  if(bmLink){
    bmLink.href = generateBookmarklet();
    // Prevent navigation — it should only be dragged
    bmLink.addEventListener('click', function(e){
      e.preventDefault();
      alert('Drag this button to your Chrome bookmark bar!\\nDo not click — drag it.');
    });
  }
}


function showTMScript(){
  const script = "// ==UserScript==\n// @name         TritoX AgencyZoom Auto-Fill\n// @namespace    http://tampermonkey.net/\n// @version      4.10-tags\n// @description  One-click AgencyZoom field fill and quote PDF attachment from TritoX QC\n// @match        https://app.agencyzoom.com/*\n// @match        https://tritoxtech.github.io/*\n// @grant        GM_setValue\n// @grant        GM_getValue\n// @grant        GM_deleteValue\n// @grant        GM_addStyle\n// @grant        unsafeWindow\n// ==/UserScript==\n\n(function(){\n  'use strict';\n\n  console.log('[TritoX TM] hostname:', window.location.hostname);\n  if(window.location.hostname === 'tritoxtech.github.io'){\n    console.log('[TritoX TM] Running on TritoX page');\n\n    // Aaron's current GitHub page omits the processed PDF filename from\n    // buildAZData(). Patch it at runtime so AgencyZoom can retrieve the exact\n    // cached PDF that belongs to the selected quote.\n    (function patchAaronFilenameTransfer(){\n      const pageWindow=typeof unsafeWindow!=='undefined'?unsafeWindow:window;\n      let attempts=0;\n      const timer=setInterval(function(){\n        attempts++;\n        const original=pageWindow.buildAZData;\n        if(typeof original==='function' && !original.__tritoxFilenamePatched){\n          const patched=function(result){\n            const data=original.apply(this,arguments);\n            if(data && result && result.filename) data._filename=result.filename;\n            return data;\n          };\n          patched.__tritoxFilenamePatched=true;\n          pageWindow.buildAZData=patched;\n          clearInterval(timer);\n          console.log('[TritoX TM] Aaron filename transfer patch installed');\n        }else if(original && original.__tritoxFilenamePatched){\n          clearInterval(timer);\n        }else if(attempts>=80){\n          clearInterval(timer);\n          console.warn('[TritoX TM] Aaron buildAZData was not found');\n        }\n      },250);\n    })();\n\n    function pdfStorageKey(name){\n      let h=2166136261;\n      const s=String(name||'quote.pdf').toLowerCase();\n      for(let i=0;i\u003cs.length;i++){\n        h^=s.charCodeAt(i);\n        h=Math.imul(h,16777619);\n      }\n      return 'tritox_pdf_'+(h>>>0).toString(16);\n    }\n\n    function cachePdf(file){\n      if(!file || !/.pdf$/i.test(file.name)) return;\n      if(file.size>20*1024*1024){\n        console.warn('[TritoX TM] PDF is larger than 20 MB and was not cached:',file.name);\n        return;\n      }\n      const reader=new FileReader();\n      reader.onload=function(){\n        try{\n          GM_setValue(pdfStorageKey(file.name),JSON.stringify({\n            name:file.name,\n            type:file.type||'application/pdf',\n            size:file.size,\n            lastModified:file.lastModified||Date.now(),\n            dataUrl:String(reader.result),\n            savedAt:Date.now()\n          }));\n          console.log('[TritoX TM] PDF cached for AgencyZoom:',file.name);\n        }catch(err){\n          console.error('[TritoX TM] Could not cache PDF:',err);\n        }\n      };\n      reader.readAsDataURL(file);\n    }\n\n    // Capture PDFs selected or dropped into Aaron QC. Each file is stored under\n    // its filename so bulk processing can still match the correct customer PDF.\n    document.addEventListener('change',function(e){\n      if(e.target && e.target.id==='fileInput' && e.target.files){\n        Array.from(e.target.files).forEach(cachePdf);\n      }\n    },true);\n    document.addEventListener('drop',function(e){\n      if(e.dataTransfer && e.dataTransfer.files && e.target.closest && e.target.closest('#uploadZone')){\n        Array.from(e.dataTransfer.files).forEach(cachePdf);\n      }\n    },true);\n\n    function checkTritoXData(){\n      try{\n        const raw = localStorage.getItem('tritox_az_data');\n        if(!raw) return;\n        const data = JSON.parse(raw);\n        if(!data || !data._name || !data._ts) return;\n        const gmRaw = GM_getValue('tritox_az_data','');\n        let gmTs = 0;\n        try{ gmTs = JSON.parse(gmRaw)._ts || 0; }catch(e){}\n        if(data._ts > gmTs){\n          GM_setValue('tritox_az_data', JSON.stringify(data));\n          console.log('[TritoX TM] Saved to GM for:', data._name);\n        }\n      }catch(e){ console.log('[TritoX TM] Error:', e); }\n    }\n    setInterval(checkTritoXData, 1000);\n    return;\n  }\n\n  console.log('[TritoX TM] Running on AgencyZoom page');\n  function addFillButton(){\n    const vehicleField = document.getElementById('customfields-cf30203');\n    const leadPanel = document.getElementById('referral-container');\n    if(!vehicleField && !leadPanel) return;\n    if(document.getElementById('tritox-fill-btn')) return;\n    const raw = GM_getValue('tritox_az_data','');\n    if(!raw) return;\n    let data;\n    try{ data = JSON.parse(raw); }catch(e){ return; }\n    if(!data || !data._name) return;\n    const buttonDataTs = data._ts || 0;\n\n    // Do not recreate a button that was already filled or expired.\n    if(buttonDataTs \u003c= filledTs || buttonDataTs \u003c= expiredTs) return;\n\n    const age = Date.now() - buttonDataTs;\n    if(age > 7200000) return;\n\n    const btn = document.createElement('div');\n    btn.id = 'tritox-fill-btn';\n    btn.style.cssText = 'position:fixed;top:80px;right:20px;z-index:99999;background:linear-gradient(135deg,#00d4ff,#7b2fff);color:#fff;padding:10px 16px;border-radius:10px;cursor:pointer;font-size:13px;font-weight:700;box-shadow:0 4px 20px rgba(0,212,255,0.4);font-family:sans-serif;text-align:center;min-width:160px;';\n    btn.innerHTML = '🚀 Fill + Attach PDF\u003cbr>\u003cspan style=\"font-size:11px;font-weight:400;opacity:0.9;\">' + data._name + '\u003c/span>';\n\n    // Initial Fill button is visible for a maximum of 7 seconds if untouched.\n    let autoHideTimer = null;\n\n    btn.addEventListener('click', function(){\n      // User interacted in time, so stop the idle 7-second timer.\n      if(autoHideTimer){\n        clearTimeout(autoHideTimer);\n        autoHideTimer = null;\n      }\n\n      // Show confirmation popup\n      const overlay = document.createElement('div');\n      overlay.style.cssText = 'position:fixed;inset:0;background:rgba(0,0,0,0.7);z-index:999999;display:flex;align-items:center;justify-content:center;';\n      const box = document.createElement('div');\n      box.style.cssText = 'background:#fff;border-radius:16px;padding:28px 32px;max-width:380px;width:90%;text-align:center;font-family:sans-serif;box-shadow:0 20px 60px rgba(0,0,0,0.3);';\n      box.innerHTML = '\u003cdiv style=\"font-size:32px;margin-bottom:12px;\">⚠️\u003c/div>'\n        +'\u003cdiv style=\"font-size:16px;font-weight:700;color:#1a1a2e;margin-bottom:8px;\">Confirm Fill + PDF Attachment\u003c/div>'\n        +'\u003cdiv style=\"font-size:13px;color:#666;margin-bottom:6px;\">You are about to fill and attach the quote PDF for:\u003c/div>'\n        +'\u003cdiv style=\"font-size:15px;font-weight:700;color:#7b2fff;margin-bottom:20px;padding:10px;background:#f0e8ff;border-radius:8px;\">'+data._name+'\u003c/div>'\n        +'\u003cdiv style=\"font-size:12px;color:#999;margin-bottom:20px;\">Make sure you are on the correct lead in AgencyZoom before confirming.\u003c/div>'\n        +'\u003cdiv style=\"display:flex;gap:10px;justify-content:center;\">'\n        +'\u003cbutton id=\"tritox-cancel\" style=\"flex:1;padding:10px;border:2px solid #ddd;background:#fff;border-radius:8px;cursor:pointer;font-size:13px;font-weight:600;color:#666;\">❌ Cancel\u003c/button>'\n        +'\u003cbutton id=\"tritox-confirm\" style=\"flex:1;padding:10px;border:none;background:linear-gradient(135deg,#00d4ff,#7b2fff);border-radius:8px;cursor:pointer;font-size:13px;font-weight:700;color:#fff;\">✅ Fill + Attach\u003c/button>'\n        +'\u003c/div>';\n      overlay.appendChild(box);\n      document.body.appendChild(overlay);\n\n      // Cancel\n      document.getElementById('tritox-cancel').addEventListener('click', function(){\n        overlay.remove();\n        expiredTs = Math.max(expiredTs, buttonDataTs);\n        GM_setValue('tritox_expired_ts', expiredTs);\n        if(btn.isConnected) btn.remove();\n      });\n\n      // Confirm\n      document.getElementById('tritox-confirm').addEventListener('click', async function(){\n        overlay.remove();\n        btn.style.background='linear-gradient(135deg,#0085ff,#7b2fff)';\n        btn.innerHTML='⏳ Attaching PDF…\u003cbr>\u003cspan style=\"font-size:11px;font-weight:400;opacity:0.9;\">'+data._name+'\u003c/span>';\n\n        const attachResult=await attachPdfToLead(data);\n        await openLeadTab('Main');\n        await waitFor(function(){return document.getElementById('customfields-cf30203');},12000);\n        fillFields(data);\n        const tagResult=await applyQuoteTags(data);\n        // Store timestamp of this fill to prevent reappearing\n        try{\n          const d = JSON.parse(GM_getValue('tritox_az_data','{}'));\n          filledTs = d._ts || Date.now();\n        }catch(e){ filledTs = Date.now(); }\n        GM_setValue('tritox_az_data','');\n        if(attachResult.ok && data._filename){\n          GM_deleteValue(pdfStorageKey(data._filename));\n        }\n\n        // Show done state with close button and countdown\n        let secs = 8;\n        btn.style.background = attachResult.ok && tagResult.ok ? 'linear-gradient(135deg,#00e887,#00b359)' : '#a65b00';\n        btn.style.minWidth = '180px';\n\n        function updateBtn(){\n          btn.innerHTML = (attachResult.ok && tagResult.ok?'✅ Filled + PDF + Tags':'⚠️ Filled — Review PDF / Tags')+'\u003cbr>'\n            +'\u003cspan style=\"font-size:11px;font-weight:400;opacity:0.9;\">' + data._name + '\u003c/span>\u003cbr>'\n            +'\u003cspan style=\"font-size:10px;font-weight:400;opacity:0.9;\">'+attachResult.message+'\u003c/span>\u003cbr>'\n            +'\u003cspan style=\"font-size:10px;font-weight:400;opacity:0.9;\">'+tagResult.message+'\u003c/span>\u003cbr>'\n            +'\u003cdiv style=\"display:flex;align-items:center;justify-content:center;gap:8px;margin-top:6px;\">'\n            +'\u003cspan style=\"font-size:10px;opacity:0.8;\">Auto close in '+secs+'s\u003c/span>'\n            +'\u003cbutton id=\"tritox-close-btn\" style=\"background:rgba(255,255,255,0.25);border:1px solid rgba(255,255,255,0.5);color:#fff;border-radius:6px;padding:2px 8px;cursor:pointer;font-size:11px;font-weight:700;\">✕ Close\u003c/button>'\n            +'\u003c/div>';\n\n          // Attach close button listener after innerHTML update\n          const closeBtn = document.getElementById('tritox-close-btn');\n          if(closeBtn){\n            closeBtn.addEventListener('click', function(e){\n              e.stopPropagation();\n              btn.remove();\n            });\n          }\n        }\n\n        updateBtn();\n\n        // Countdown timer\n        const timer = setInterval(function(){\n          secs--;\n          if(secs \u003c= 0){\n            clearInterval(timer);\n            btn.remove();\n          } else {\n            updateBtn();\n          }\n        }, 1000);\n      });\n\n      // Click outside to cancel\n      overlay.addEventListener('click', function(e){\n        if(e.target === overlay){\n          overlay.remove();\n          expiredTs = Math.max(expiredTs, buttonDataTs);\n          GM_setValue('tritox_expired_ts', expiredTs);\n          if(btn.isConnected) btn.remove();\n        }\n      });\n    });\n\n    document.body.appendChild(btn);\n\n    // If untouched, automatically remove the Fill button after 7 seconds.\n    // Save the expired timestamp so the 1.5-second checker cannot recreate it.\n    autoHideTimer = setTimeout(function(){\n      if(btn.isConnected){\n        btn.remove();\n      }\n      expiredTs = Math.max(expiredTs, buttonDataTs);\n      GM_setValue('tritox_expired_ts', expiredTs);\n    }, 7000);\n  }\n\n  function pdfStorageKey(name){\n    let h=2166136261;\n    const s=String(name||'quote.pdf').toLowerCase();\n    for(let i=0;i\u003cs.length;i++){\n      h^=s.charCodeAt(i);\n      h=Math.imul(h,16777619);\n    }\n    return 'tritox_pdf_'+(h>>>0).toString(16);\n  }\n\n  function wait(ms){return new Promise(function(resolve){setTimeout(resolve,ms);});}\n\n  async function waitFor(getter,timeout){\n    const start=Date.now();\n    while(Date.now()-start\u003ctimeout){\n      const value=getter();\n      if(value) return value;\n      await wait(250);\n    }\n    return null;\n  }\n\n  function findLeadTab(label){\n    const wanted=String(label).toLowerCase();\n    const match=Array.from(document.querySelectorAll('#referral-container a,#referral-container button,#referral-container [role=\"tab\"],#referral-container li,#referral-container span,#referral-container div'))\n      .find(function(el){return (el.textContent||'').trim().toLowerCase()===wanted;})||null;\n    return match ? (match.closest('a,button,li,[role=\"tab\"]')||match) : null;\n  }\n\n  async function openLeadTab(label){\n    const tab=findLeadTab(label);\n    if(!tab) return false;\n    tab.click();\n    await wait(900);\n    return true;\n  }\n\n  function payloadToFile(payload){\n    const parts=String(payload.dataUrl||'').split(',');\n    if(parts.length\u003c2) throw new Error('Stored PDF data is incomplete');\n    const bytes=atob(parts[1]);\n    const array=new Uint8Array(bytes.length);\n    for(let i=0;i\u003cbytes.length;i++) array[i]=bytes.charCodeAt(i);\n    return new File([array],payload.name,{\n      type:payload.type||'application/pdf',\n      lastModified:payload.lastModified||Date.now()\n    });\n  }\n\n  function findAgencyZoomFileInput(){\n    const selectors=[\n      '#referral-container input[type=\"file\"]',\n      '.agencydocupload_doc input[type=\"file\"]',\n      '#agencyDocUploader input[type=\"file\"]',\n      'input.agencydocupload_doc[type=\"file\"]',\n      'input[type=\"file\"][multiple]',\n      'input[type=\"file\"]'\n    ];\n    for(const selector of selectors){\n      const inputs=Array.from(document.querySelectorAll(selector));\n      if(inputs.length) return inputs[inputs.length-1];\n    }\n    return null;\n  }\n\n  function pdfNameIsVisible(fileName){\n    const wanted=String(fileName||'').toLowerCase();\n    const panel=document.getElementById('referral-container');\n    if(!panel || !wanted) return false;\n    if((panel.innerText||'').toLowerCase().includes(wanted)) return true;\n    return Array.from(panel.querySelectorAll('[title],[data-name],[data-file-name],a'))\n      .some(function(el){\n        return [el.getAttribute('title'),el.getAttribute('data-name'),el.getAttribute('data-file-name'),el.textContent]\n          .some(function(value){return String(value||'').toLowerCase().includes(wanted);});\n      });\n  }\n\n  function getCurrentLeadId(){\n    // The visible \"ID: 12345678\" is in AgencyZoom's lead header, outside\n    // #referral-container. Search the complete rendered page first.\n    const texts=[\n      document.body&&document.body.innerText,\n      document.documentElement&&document.documentElement.innerText,\n      document.getElementById('referral-container')&&document.getElementById('referral-container').innerText\n    ];\n    for(const text of texts){\n      const match=String(text||'').match(/\\bID\\s*:\\s*(\\d{5,})\\b/i);\n      if(match) return match[1];\n    }\n\n    const selectors=[\n      '[data-entity-id]','[data-entityid]','[data-lead-id]','[data-leadid]',\n      'input[name=\"entityId\"]','input[name=\"leadId\"]'\n    ];\n    for(const selector of selectors){\n      const el=document.querySelector(selector);\n      if(!el) continue;\n      const value=el.value||el.getAttribute('data-entity-id')||el.getAttribute('data-entityid')||\n        el.getAttribute('data-lead-id')||el.getAttribute('data-leadid');\n      if(/^\\d{5,}$/.test(String(value||''))) return String(value);\n    }\n\n    // Final fallback: AgencyZoom often embeds the active lead ID in its\n    // uploader configuration even when the header has not finished rendering.\n    const html=document.documentElement&&document.documentElement.innerHTML||'';\n    const configMatch=html.match(/(?:entityId|leadId)[\"']?\\s*[:=]\\s*[\"']?(\\d{5,})/i);\n    if(configMatch) return configMatch[1];\n    return '';\n  }\n\n  async function uploadPdfDirect(file,leadId){\n    const pageWindow=typeof unsafeWindow!=='undefined'?unsafeWindow:window;\n    const jq=pageWindow.jQuery;\n    if(!jq || typeof jq.ajax!=='function') throw new Error('AgencyZoom uploader session was not ready');\n\n    const query=new URLSearchParams({\n      docType:'undefined',\n      fileName:file.name,\n      docuSign:'0'\n    });\n    // Create the file and FormData in AgencyZoom's own page context. This lets\n    // its jQuery AJAX configuration apply the same session/security handling\n    // used by the successful manual uploader.\n    const bytes=await file.arrayBuffer();\n    const pageFile=new pageWindow.File([bytes],file.name,{\n      type:file.type||'application/pdf',\n      lastModified:file.lastModified||Date.now()\n    });\n    const form=new pageWindow.FormData();\n    form.append('contacts','[]');\n    form.append('emailSubject','');\n    form.append('emailBody','');\n    form.append('entityId',String(leadId));\n    form.append('linkToType','lead');\n    form.append('files[]',pageFile,pageFile.name);\n\n    return new Promise(function(resolve,reject){\n      jq.ajax({\n        url:'/lead/doc?'+query.toString(),\n        type:'POST',\n        data:form,\n        processData:false,\n        contentType:false,\n        cache:false,\n        success:function(result){\n          if(result && result.docName && String(result.docName)!==file.name){\n            reject(new Error('AgencyZoom returned a different filename'));\n            return;\n          }\n          resolve(result);\n        },\n        error:function(xhr){\n          let detail='HTTP '+(xhr&&xhr.status||'error');\n          try{\n            const body=xhr.responseJSON||JSON.parse(xhr.responseText||'{}');\n            detail=body.message||body.error||detail;\n          }catch(e){}\n          reject(new Error(detail));\n        }\n      });\n    });\n  }\n\n  async function attachPdfToLead(data){\n    if(!data._filename) return {ok:false,message:'PDF filename was not transferred'};\n    const stored=GM_getValue(pdfStorageKey(data._filename),'');\n    if(!stored) return {ok:false,message:'PDF was not cached — select it again in Aaron QC'};\n\n    let payload;\n    try{payload=JSON.parse(stored);}catch(e){return {ok:false,message:'Stored PDF could not be read'};}\n    if(payload.name!==data._filename) return {ok:false,message:'PDF filename mismatch — attachment stopped'};\n\n    const file=payloadToFile(payload);\n    const leadId=getCurrentLeadId();\n    if(!leadId) return {ok:false,message:'AgencyZoom lead ID was not found'};\n\n    // Use the same multipart request captured from AgencyZoom's manual uploader.\n    await openLeadTab('Files');\n    await waitFor(function(){return document.getElementById('referral-container');},5000);\n\n    let uploadResult;\n    try{\n      uploadResult=await uploadPdfDirect(file,leadId);\n    }catch(err){\n      console.error('[TritoX TM] Direct PDF upload failed:',err);\n      return {ok:false,message:'AgencyZoom rejected PDF upload: '+String(err.message||err)};\n    }\n\n    // AgencyZoom returns the saved document object immediately. Its Files list\n    // can take several seconds to refresh, so do not show a false error or hold\n    // up field filling while waiting for the UI.\n    if(uploadResult && (uploadResult.id || uploadResult.docName || uploadResult.dest)){\n      await wait(500);\n      return {ok:true,message:'PDF uploaded successfully to AgencyZoom'};\n    }\n    // Some accounts return an empty 200 response. A quick visible-name check is\n    // enough; never run the previous long refresh/retry sequence.\n    await wait(900);\n    return pdfNameIsVisible(file.name)\n      ? {ok:true,message:'PDF uploaded successfully to AgencyZoom'}\n      : {ok:true,message:'AgencyZoom accepted the PDF upload'};\n  }\n\n  function fillText(id, val){\n    if(!val && val !== 0) return;\n    const el = document.getElementById('customfields-' + id);\n    if(!el) return;\n    try{\n      el.focus();\n      const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set;\n      setter.call(el, String(val));\n      el.dispatchEvent(new Event('focus',{bubbles:true}));\n      el.dispatchEvent(new Event('input',{bubbles:true}));\n      el.dispatchEvent(new Event('change',{bubbles:true}));\n      el.dispatchEvent(new KeyboardEvent('keydown',{bubbles:true}));\n      el.dispatchEvent(new KeyboardEvent('keyup',{bubbles:true}));\n      el.blur();\n      el.dispatchEvent(new Event('blur',{bubbles:true}));\n    }catch(e){}\n  }\n\n  function fillSelect(id, val){\n    if(!val) return;\n    const el = document.getElementById('customfields-' + id);\n    if(!el) return;\n    try{\n      const setter = Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,'value').set;\n      setter.call(el, String(val));\n      el.dispatchEvent(new Event('change',{bubbles:true}));\n    }catch(e){}\n  }\n\n  function quoteTagNames(data){\n    if(typeof data._isBundle !== 'boolean') throw new Error('Reprocess the PDF in the updated Aaron QC to transfer quote type');\n    return data._isBundle ? ['Ready to send','Home is quoted'] : ['Ready to send'];\n  }\n\n  async function applyQuoteTags(data){\n    let toggle=null;\n    let opened=false;\n    try{\n      const names=quoteTagNames(data);\n      const candidates=Array.from(document.querySelectorAll('select[name=\"tags[]\"][multiple]'))\n        .filter(function(select){\n          const wrapper=select.closest('.bootstrap-select');\n          return wrapper && wrapper.getClientRects().length &&\n            names.every(function(name){return Array.from(select.options).some(function(o){return o.textContent.trim()===name;});});\n        });\n      if(candidates.length!==1) throw new Error('Open the lead tag selector and add the quote tags manually; no unique visible selector found');\n      const select=candidates[0];\n      const wrapper=select.closest('.bootstrap-select');\n      toggle=wrapper.querySelector('button.dropdown-toggle');\n      if(!toggle) throw new Error('Lead tag dropdown button not found');\n      const before=Array.from(select.selectedOptions).map(function(o){return o.value;});\n      if(toggle.getAttribute('aria-expanded')!=='true'){\n        toggle.click();\n        opened=true;\n        await wait(200);\n      }\n      // Resolve generated list IDs from this lead's own button, never page filters.\n      const listId=toggle.getAttribute('aria-owns')||toggle.getAttribute('aria-controls');\n      const list=(listId && document.getElementById(listId))||wrapper;\n      for(const name of names){\n        const option=Array.from(select.options).find(function(o){return o.textContent.trim()===name;});\n        if(option.selected) continue;\n        const item=Array.from(list.querySelectorAll('a[role=\"option\"]')).find(function(el){return el.textContent.trim()===name;});\n        if(!item || option.disabled || item.getAttribute('aria-disabled')==='true') throw new Error('Tag unavailable: '+name);\n        // A real option click invokes AgencyZoom's normal selection/save handlers.\n        item.click();\n        await wait(350);\n        if(!option.selected) throw new Error('Tag selection was not confirmed: '+name);\n      }\n      if(!before.every(function(value){return Array.from(select.selectedOptions).some(function(o){return o.value===value;});})){\n        throw new Error('Existing tags changed; review the lead tags');\n      }\n      return {ok:true,message:'Tags selected: '+names.join(' + ')};\n    }catch(error){\n      console.error('[TritoX TM] Quote tags:',error);\n      return {ok:false,message:String(error.message||error)};\n    }finally{\n      if(opened && toggle && toggle.getAttribute('aria-expanded')==='true') toggle.click();\n    }\n  }\n\n  function fillFields(d){\n    fillText('cf30203', d.vehicles_policy);\n    fillSelect('cf56698', d.bodily_injury);\n    fillText('cf47028', d.home_coverage_a);\n    fillText('cf37981', d.home_annual);\n    fillText('cf56654', d.auto1);\n    fillSelect('cf56655', d.auto1_ded);\n    fillText('cf56656', d.auto2);\n    fillSelect('cf56657', d.auto2_ded);\n    fillText('cf56692', d.auto3);\n    fillSelect('cf56693', d.auto3_ded);\n    fillText('cf56694', d.auto4);\n    fillSelect('cf56695', d.auto4_ded);\n    fillText('cf56696', d.auto5);\n    fillSelect('cf56697', d.auto5_ded);\n    function fillMoneyFields(attempt){\n      const mEl = document.querySelector('input[name=\"customFields[cf30197]\"]');\n      const sEl = document.querySelector('input[name=\"customFields[cf30199]\"]');\n      if(mEl){\n        const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set;\n        setter.call(mEl, String(d.monthly_auto).replace(/[$,]/g,''));\n        mEl.dispatchEvent(new Event('input',{bubbles:true}));\n        mEl.dispatchEvent(new Event('change',{bubbles:true}));\n      }\n      if(sEl){\n        const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set;\n        setter.call(sEl, String(d.auto_6months).replace(/[$,]/g,''));\n        sEl.dispatchEvent(new Event('input',{bubbles:true}));\n        sEl.dispatchEvent(new Event('change',{bubbles:true}));\n      }\n      if((!mEl || !sEl) && attempt \u003c 30){\n        setTimeout(function(){ fillMoneyFields(attempt+1); }, 800);\n      }\n    }\n    fillMoneyFields(1);\n  }\n\n  let lastCheck = '';\n  let filledTs = 0; // timestamp of last fill action\n\n  // Timestamp of the last Fill button that expired/cancelled.\n  // Persisted so the same old PDF does not reappear after an AgencyZoom reload.\n  let expiredTs = Number(GM_getValue('tritox_expired_ts', 0)) || 0;\n  \n  setInterval(function(){\n    // Get current GM data\n    const gmRaw = GM_getValue('tritox_az_data','');\n    if(!gmRaw){\n      // No data — button should not show\n      return;\n    }\n    \n    let gmData;\n    try{ gmData = JSON.parse(gmRaw); }catch(e){ return; }\n    \n    // If GM data is newer than last fill → new PDF processed → show button\n    const gmTs = gmData._ts || 0;\n    if(gmTs \u003c= filledTs || gmTs \u003c= expiredTs){\n      // Same or older data — already filled, cancelled, or expired.\n      return;\n    }\n    \n    // New PDF data available — show button\n    const cur = window.location.href + (document.querySelector('.az-form-group') ? '1' : '0');\n    if(cur !== lastCheck){\n      lastCheck = cur;\n      const existing = document.getElementById('tritox-fill-btn');\n      if(existing) existing.remove();\n      setTimeout(addFillButton, 1200);\n    }\n    addFillButton();\n  }, 1500);\n\n  setTimeout(addFillButton, 2000);\n\n})();\n";

  // Show modal with script
  const modal = document.createElement('div');
  modal.style.cssText = 'position:fixed;inset:0;background:rgba(0,0,0,0.85);z-index:9999;display:flex;align-items:center;justify-content:center;padding:20px;';
  const box = document.createElement('div');
  box.style.cssText = 'background:#111520;border:1px solid #252d45;border-radius:16px;max-width:700px;width:100%;max-height:90vh;overflow-y:auto;padding:28px;';
  box.innerHTML = '<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:16px;">'
    +'<h3 style="font-family:monospace;color:#00d4ff;font-size:15px;">📜 Tampermonkey Script</h3>'
    +'<button id="tmClose" style="background:#1e2438;border:1px solid #252d45;color:#dce4f5;border-radius:8px;padding:6px 14px;cursor:pointer;">✕ Close</button>'
    +'</div>'
    +'<div style="color:#8a97bb;font-size:12px;margin-bottom:12px;line-height:1.7;">'
    +'<strong style="color:#dce4f5;">Setup Steps:</strong><br>'
    +'1. Install <a href="https://www.tampermonkey.net/" target="_blank" style="color:#00d4ff;">Tampermonkey</a> from Chrome Web Store (free)<br>'
    +'2. Open Tampermonkey Dashboard → edit TritoX AgencyZoom Auto-Fill (or create a new script if not installed)<br>'
    +'3. Select all existing code → Delete<br>'
    +'4. Copy script below → Paste → Ctrl+S to Save<br>'
    +'5. Refresh Aaron QC and AgencyZoom, then select your PDF again. On the correct lead, click 🚀 Fill + Attach PDF.'
    +'</div>'
    +'<button id="tmCopyBtn" style="background:linear-gradient(135deg,#00d4ff,#7b2fff);color:#fff;border:none;border-radius:8px;padding:8px 18px;font-size:12px;font-weight:700;cursor:pointer;margin-bottom:12px;">📋 Copy Script</button>'
    +'<pre id="tmScriptPre" style="background:#080a0f;border:1px solid #252d45;border-radius:8px;padding:14px;font-size:11px;color:#dce4f5;white-space:pre-wrap;overflow-x:auto;max-height:350px;overflow-y:auto;"></pre>';

  modal.appendChild(box);
  document.body.appendChild(modal);

  // Set script text safely
  document.getElementById('tmScriptPre').textContent = script;

  // Copy button
  document.getElementById('tmCopyBtn').addEventListener('click', function(){
    navigator.clipboard.writeText(script).then(function(){
      document.getElementById('tmCopyBtn').textContent = '✅ Copied!';
      setTimeout(function(){ document.getElementById('tmCopyBtn').textContent = '📋 Copy Script'; }, 2000);
    });
  });

  // Close
  document.getElementById('tmClose').addEventListener('click', function(){ modal.remove(); });
  modal.addEventListener('click', function(e){ if(e.target===modal) modal.remove(); });
}

// ── UPDATE Tampermonkey data save ── 
// After processing, also save to Tampermonkey via a special localStorage key
// that the Tampermonkey script will read
function saveTMData(data){
  // We store in a special key that Tampermonkey can access
  // via GM_setValue bridge - this is done through a hidden element
  try{
    // Create/update a hidden meta tag with the data
    let meta = document.getElementById('tritox-tm-bridge');
    if(!meta){
      meta = document.createElement('meta');
      meta.id = 'tritox-tm-bridge';
      meta.name = 'tritox-data';
      document.head.appendChild(meta);
    }
    meta.content = JSON.stringify(data);
  } catch(e){}
}

function updateSummary(){
  document.getElementById('scTotal').textContent=allResults.length;
  document.getElementById('scPass').textContent=allResults.filter(r=>r.status==='pass').length;
  document.getElementById('scFail').textContent=allResults.filter(r=>r.status==='fail').length;
  document.getElementById('scWarn').textContent=allResults.filter(r=>r.status==='warn').length;
}

function exportCSV(type){
  const data=type==='flagged'?allResults.filter(r=>r.status==='fail'):allResults;
  if(!data.length){alert('No results to export.');return;}
  const rows=[['Customer','Filename','Type','Vehicles','Monthly EFT','Status','Errors','Warnings']];
  data.forEach(r=>rows.push([r.name,r.filename,r.quoteType,r.vehicleCount,
    r.monthlyEFT?'$'+r.monthlyEFT.toFixed(2):'',r.status.toUpperCase(),
    r.errors.join(' | '),r.warnings.join(' | ')]));
  const csv=rows.map(r=>r.map(c=>`"${String(c).replace(/"/g,'""')}"`).join(',')).join('\n');
  const a=document.createElement('a');
  a.href=URL.createObjectURL(new Blob([csv],{type:'text/csv'}));
  a.download=`TritoX_QC_${type}_${new Date().toISOString().slice(0,10)}.csv`;
  a.click();
}

function clearAll(){
  allResults=[];
  document.getElementById('resultsBody').innerHTML='';
  document.getElementById('summaryBar').style.display='none';
  document.getElementById('toolbar').style.display='none';
  document.getElementById('resultsWrap').style.display='none';
  updateSummary();
}
</script>
</body>
</html>
