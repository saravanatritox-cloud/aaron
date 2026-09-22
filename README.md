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
      <a class="back-link" href="https://saravanatritox-cloud.github.io/" aria-label="Back to Saravanan home" title="Back to Saravanan home">&#8592;</a>
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
        <span class="brand-badge">v4.34 STABLE</span>
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
    _highPrice:!!r.putInStop,
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
  const script = "// ==UserScript==\n// @name         TritoX AgencyZoom Auto-Fill\n// @namespace    http://tampermonkey.net/\n// @version      4.48-vehicle-card-fix\n// @description  Stable rollback: original QC field fill + PDF + working automatic tag save\n// @match        https://app.agencyzoom.com/*\n// @match        https://alta.farmers.com/*\n// @match        https://tritoxtech.github.io/*\n// @match        https://saravanatritox-cloud.github.io/aaron/*\n// @grant        GM_setValue\n// @grant        GM_getValue\n// @grant        GM_deleteValue\n// @grant        GM_addStyle\n// @grant        unsafeWindow\n// ==/UserScript==\n\n(function(){\n  'use strict';\n\n  console.log('[TritoX TM] v4.48 vehicle card fix hostname:', window.location.hostname);\n\n\n  // ────────────────────────────────────────────────────────────────────────────\n  // ALTA — capture extra lead metadata once and keep it while navigating pages.\n  // Customer name + ALTA ID are read automatically; the user only confirms the\n  // carrier, renewal date and Star/BW value.\n  // ────────────────────────────────────────────────────────────────────────────\n  if(window.location.hostname === 'alta.farmers.com'){\n    const CARRIERS=[\n      'AAA','Allstate','Auto-Owners','Bristol West','Farm Bureau','GEICO',\n      'Liberty Mutual','Nationwide','Progressive','State Farm','Travelers','USAA'\n    ];\n\n    function cleanText(v){ return String(v||'').replace(/\\s+/g,' ').trim(); }\n    function normName(v){ return cleanText(v).toLowerCase().replace(/[^a-z0-9]+/g,' ').trim(); }\n\n    function altaIdentity(){\n      const text=document.body ? document.body.innerText : '';\n      const idm=text.match(/Alta\\s*#\\s*(\\d{8,})/i);\n      const id=idm?idm[1]:'';\n      let name='';\n      const nm=text.match(/(?:^|\\n)\\s*([^\\n]{2,80}?)\\s*-\\s*Auto\\s*(?:\\n|$)/i);\n      if(nm) name=cleanText(nm[1]);\n      if(!name){\n        const nm2=text.match(/([A-Za-z][A-Za-z .'-]{2,70})\\s*-\\s*Auto\\s+Alta\\s*#/i);\n        if(nm2) name=cleanText(nm2[1]);\n      }\n      return {name,id};\n    }\n\n    const ALTA_META_TTL=7200000; // same 2-hour validity window as Aaron autofill popup\n\n    function priorInsuranceSnapshot(){\n      const text=document.body ? document.body.innerText : '';\n      const start=text.search(/Prior insurance information/i);\n      if(start<0) return {visible:false,inEffect:false,company:'',renewalDate:''};\n      const chunk=text.slice(start,start+3000);\n\n      // ALTA can show several prior policies. Always use the TOP/FIRST policy\n      // that is explicitly marked \"In Effect\". Do not choose a carrier merely\n      // because its name appears somewhere later in the prior-insurance list.\n      const lines=chunk.split(/\\n+/).map(function(v){return cleanText(v);}).filter(Boolean);\n      let company='';\n      let renewalDate='';\n      let foundInEffect=false;\n\n      for(let i=0;i<lines.length;i++){\n        if(!/\\bIn Effect\\b/i.test(lines[i])) continue;\n        foundInEffect=true;\n\n        // Build a small row window around this FIRST In Effect marker. In ALTA,\n        // the carrier/status/date may be on one line or split across nearby lines.\n        const from=Math.max(0,i-2);\n        const to=Math.min(lines.length,i+4);\n        const row=lines.slice(from,to).join(' ');\n\n        // Prefer the carrier whose text is physically closest to this row.\n        // This preserves ALTA's on-screen order (top row wins).\n        let best=null;\n        for(const carrier of CARRIERS){\n          const rx=new RegExp('\\\\b'+carrier.replace(/[.*+?^${}()|[\\]\\\\]/g,'\\\\$&')+'\\\\b','i');\n          const m=rx.exec(row);\n          if(m && (!best || m.index<best.index)) best={name:carrier,index:m.index};\n        }\n        if(best) company=best.name;\n\n        if(!company){\n          // Generic fallback: take text immediately before \"In Effect\" from\n          // the same line, then clean off table labels/numbers if present.\n          const same=lines[i].match(/^(.{2,80}?)\\s+In Effect\\b/i);\n          if(same){\n            let candidate=cleanText(same[1]);\n            candidate=candidate.replace(/^(?:Prior insurance information|Driver|Drivers|Vehicles|Tenure|Coverage Term|BI|PD)\\s*/i,'').trim();\n            candidate=candidate.replace(/^\\d+\\s+/,'').trim();\n            if(candidate) company=candidate;\n          }\n        }\n\n        // Renewal date must come from the SAME first In Effect policy.\n        // ALTA often renders the carrier/status on one DOM line and the\n        // Coverage Term several lines later, so the old 4-line window could\n        // miss the date even though the top policy was detected correctly.\n        let dm=row.match(/\\b(\\d{1,2}\\/\\d{1,2}\\/\\d{4})\\s*-\\s*(\\d{1,2}\\/\\d{1,2}\\/\\d{4})\\b/);\n        if(!dm){\n          const firstEffectPos=chunk.search(/\\bIn Effect\\b/i);\n          if(firstEffectPos>=0){\n            const afterFirst=chunk.slice(firstEffectPos);\n            const nextRel=afterFirst.slice(1).search(/\\bIn Effect\\b/i);\n            const firstPolicyText=nextRel>=0\n              ? afterFirst.slice(0,nextRel+1)\n              : afterFirst.slice(0,700);\n            dm=firstPolicyText.match(/\\b(\\d{1,2}\\/\\d{1,2}\\/\\d{4})\\s*-\\s*(\\d{1,2}\\/\\d{1,2}\\/\\d{4})\\b/);\n          }\n        }\n        if(dm) renewalDate=dm[2];\n        break; // critical: never fall through to Progressive/another lower row\n      }\n\n      if(!foundInEffect) return {visible:true,inEffect:false,company:'',renewalDate:''};\n      return {visible:true,inEffect:true,company:company,renewalDate:renewalDate};\n    }\n\n    function detectCarrier(){ return priorInsuranceSnapshot().company; }\n    function detectRenewalDate(){ return priorInsuranceSnapshot().renewalDate; }\n\n    function isBwCoveragePage(){\n      // ALTA's Bristol West coverage route is explicit. Check pathname first so\n      // page text, stale SPA content, or quote labels can never override BW.\n      return /\\/quote\\/auto\\/coverages-review-bw(?:\\/)?$/i.test(location.pathname) ||\n        /\\/quote\\/auto\\/coverages-review-bw(?:[/?#]|$)/i.test(location.href);\n    }\n\n    function onAutoCoverageSection(){\n      return isBwCoveragePage() || /\\/quote\\/auto\\/coverages-review(?:\\/)?$/i.test(location.pathname) ||\n        /\\/quote\\/auto\\/coverages-review(?:[/?#]|$)/i.test(location.href);\n    }\n\n    // ── ALTA coverage presets ────────────────────────────────────────────────\n    // Apply only after the Auto coverages page has rendered. Farmers and\n    // Bristol West use separate presets. The selected values remain visible in\n    // ALTA because the real page controls are changed and normal change events\n    // are dispatched.\n    const FARMERS_COVERAGE_PRESET=[\n      ['Bodily injury','$100,000/$300,000'],\n      ['Property damage','$100,000'],\n      ['UM/UIM - bodily injury','$100,000/$300,000']\n    ];\n    const BW_COVERAGE_PRESET=[\n      ['Bodily injury','$100,000/$300,000'],\n      ['Property damage','$100,000'],\n      ['Limited property damage','$3,000'],\n      ['Uninsured motorist - bodily injury','$100,000/$300,000'],\n      ['Underinsured motorist - bodily injury','$100,000/$300,000']\n    ];\n\n    function covNorm(v){\n      return cleanText(v).toLowerCase().replace(/\\$/g,'').replace(/,/g,'').replace(/\\s+/g,'').replace(/[–—]/g,'-');\n    }\n    function covLabelNorm(v){\n      return cleanText(v).toLowerCase().replace(/[^a-z0-9/]+/g,' ').trim();\n    }\n    function visibleEl(el){\n      if(!el) return false;\n      try{\n        const r=el.getBoundingClientRect();\n        const s=getComputedStyle(el);\n        return r.width>0 && r.height>0 && s.display!=='none' && s.visibility!=='hidden';\n      }catch(e){ return false; }\n    }\n\n    function coverageSelectForLabel(label){\n      const wanted=covLabelNorm(label);\n      let best=null;\n      for(const sel of Array.from(document.querySelectorAll('select'))){\n        if(!visibleEl(sel) && !visibleEl(sel.parentElement)) continue;\n        let node=sel.parentElement;\n        for(let depth=0;node && depth<6;depth++,node=node.parentElement){\n          const txt=covLabelNorm(node.innerText||'');\n          if(txt.includes(wanted)){\n            const noise=Math.max(0,txt.length-wanted.length);\n            const score=depth*100+noise;\n            if(!best || score<best.score) best={el:sel,score:score};\n            break;\n          }\n        }\n      }\n      return best?best.el:null;\n    }\n\n    function optionForValue(select,wanted){\n      const wn=covNorm(wanted);\n      const opts=Array.from(select.options||[]);\n      return opts.find(function(o){return covNorm(o.textContent||o.label||o.value)===wn;}) ||\n        opts.find(function(o){return covNorm(o.value)===wn;}) || null;\n    }\n\n    function setCoverageNative(select,wanted){\n      if(!select) return false;\n      const opt=optionForValue(select,wanted);\n      if(!opt) return false;\n      if(String(select.value)===String(opt.value) && opt.selected) return true;\n      try{\n        const setter=Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,'value');\n        if(setter&&setter.set) setter.set.call(select,opt.value); else select.value=opt.value;\n        Array.from(select.options||[]).forEach(function(o){o.selected=(o===opt);});\n        select.dispatchEvent(new Event('input',{bubbles:true}));\n        select.dispatchEvent(new Event('change',{bubbles:true}));\n        select.dispatchEvent(new Event('blur',{bubbles:true}));\n        return true;\n      }catch(e){\n        console.warn('[TritoX TM] ALTA coverage native select failed:',wanted,e);\n        return false;\n      }\n    }\n\n    function coverageContainerForLabel(label){\n      const wanted=covLabelNorm(label);\n      const els=Array.from(document.querySelectorAll('label,div,span,p,td'));\n      let best=null;\n      for(const el of els){\n        if(!visibleEl(el)) continue;\n        const own=covLabelNorm(el.textContent||'');\n        if(own!==wanted) continue;\n        let node=el.parentElement;\n        for(let depth=0;node && depth<5;depth++,node=node.parentElement){\n          const controls=node.querySelectorAll('select,button,[role=\"combobox\"],input');\n          if(controls.length){\n            const score=depth*100+(node.innerText||'').length;\n            if(!best||score<best.score) best={el:node,score:score};\n            break;\n          }\n        }\n      }\n      return best?best.el:null;\n    }\n\n    async function setCoverageFallback(label,wanted){\n      const row=coverageContainerForLabel(label);\n      if(!row) return false;\n      const control=Array.from(row.querySelectorAll('button,[role=\"combobox\"]')).find(visibleEl);\n      if(!control) return false;\n      try{ control.click(); }catch(e){ return false; }\n      await new Promise(function(resolve){setTimeout(resolve,90);});\n      const wn=covNorm(wanted);\n      const options=Array.from(document.querySelectorAll('[role=\"option\"],mat-option,.mat-option,.dropdown-menu li a,.dropdown-menu li button,li[role=\"option\"]'))\n        .filter(visibleEl);\n      const target=options.find(function(el){return covNorm(el.textContent||'')===wn;});\n      if(!target) return false;\n      try{ target.click(); return true; }catch(e){ return false; }\n    }\n\n    // ── Vehicle coverage defaults ───────────────────────────────────────────\n    // Apply per vehicle using the year shown in each ALTA vehicle card.\n    // Both Farmers and BW: 2015+ => Comp/Collision $1,000; before 2015 => Not included.\n    // BW roadside => Yes. Farmers roadside => Standard.\n    function selectHasText(select,text){\n      const wn=covNorm(text);\n      return Array.from(select&&select.options||[]).some(function(o){return covNorm(o.textContent||o.label||o.value)===wn;});\n    }\n\n    function vehicleCards(){\n      const cards=[];\n      const seen=new Set();\n      const yearRx=/^\\s*((?:19|20)\\d{2})\\b/;\n\n      // ALTA renders the row labels (Comprehensive / Collision / Roadside)\n      // outside the individual vehicle cards. Therefore a vehicle card itself\n      // may contain only the vehicle heading, VIN and dropdowns. Detect cards\n      // from the vehicle heading + the nearest small ancestor containing the\n      // vehicle's own selects instead of requiring coverage-label text inside it.\n      const headings=Array.from(document.querySelectorAll('div,span,strong,b,h1,h2,h3,h4,h5,h6,p'));\n      for(const el of headings){\n        if(!visibleEl(el)) continue;\n        const own=cleanText(el.textContent||'');\n        if(!own || own.length>180) continue;\n        const ym=own.match(yearRx);\n        if(!ym) continue;\n\n        let node=el;\n        let chosen=null;\n        for(let depth=0;node&&depth<9;depth++,node=node.parentElement){\n          const sels=Array.from(node.querySelectorAll('select')).filter(function(s){\n            return visibleEl(s)||visibleEl(s.parentElement)||visibleEl(s.closest('div'));\n          });\n          // A vehicle card normally has at least Comp + Collision and may also\n          // contain Roadside/Rental. Keep the ancestor small so a multi-vehicle\n          // parent containing every card is never selected.\n          if(sels.length>=2 && sels.length<=9){\n            const txt=cleanText(node.innerText||'');\n            const years=(txt.match(/\\b(?:19|20)\\d{2}\\b/g)||[]);\n            if(years.length<=2){ chosen=node; break; }\n          }\n        }\n        if(chosen&&!seen.has(chosen)){\n          seen.add(chosen);\n          cards.push({el:chosen,year:Number(ym[1])});\n        }\n      }\n\n      // Remove ancestor duplicates, retaining the smallest per-vehicle card.\n      const filtered=cards.filter(function(c){\n        return !cards.some(function(o){\n          return o!==c && c.el.contains(o.el) && o.el.querySelectorAll('select').length>=2;\n        });\n      });\n      console.log('[TritoX TM] Vehicle cards detected:',filtered.map(function(c){\n        return {year:c.year,selects:c.el.querySelectorAll('select').length};\n      }));\n      return filtered;\n    }\n\n    async function setVehicleSelect(select,wanted){\n      if(!select) return false;\n      // Do not fire change events again when ALTA already shows the target.\n      const current=select.options&&select.selectedIndex>=0 ? select.options[select.selectedIndex] : null;\n      if(current && covNorm(current.textContent||current.label||current.value)===covNorm(wanted)) return true;\n      if(setCoverageNative(select,wanted)) return true;\n      const parent=select.closest('div,td,section,article')||select.parentElement;\n      const control=parent&&Array.from(parent.querySelectorAll('button,[role=\\\"combobox\\\"]')).find(visibleEl);\n      if(!control) return false;\n      try{ control.click(); }catch(e){ return false; }\n      await new Promise(function(resolve){setTimeout(resolve,70);});\n      const wn=covNorm(wanted);\n      const opts=Array.from(document.querySelectorAll('[role=\\\"option\\\"],mat-option,.mat-option,.dropdown-menu li a,.dropdown-menu li button,li[role=\\\"option\\\"]')).filter(visibleEl);\n      const target=opts.find(function(x){return covNorm(x.textContent||'')===wn;});\n      if(!target) return false;\n      try{ target.click(); return true; }catch(e){ return false; }\n    }\n\n    async function applyVehicleCoverageDefaults(){\n      if(!onAutoCoverageSection()) return true;\n      const cards=vehicleCards();\n      if(!cards.length) return false; // wait until vehicle cards render\n      const bw=isBwCoveragePage();\n      let all=true;\n      for(const card of cards){\n        const sels=Array.from(card.el.querySelectorAll('select')).filter(function(s){return visibleEl(s)||visibleEl(s.parentElement);});\n        if(!sels.length){ all=false; continue; }\n        const full=card.year>=2015;\n\n        // Comprehensive is normally the first vehicle dropdown. Prefer\n        // option-signature detection, then fall back to the first select.\n        const comp=sels.find(function(s){return selectHasText(s,'$1,000')&&selectHasText(s,'Not included');}) || sels[0] || null;\n        if(comp){ if(!await setVehicleSelect(comp,full?'$1,000':'Not included')) all=false; } else all=false;\n\n        if(bw){\n          // BW collision is usually one combined selector: $1,000 Standard / Not included.\n          let coll=sels.find(function(s){\n            return s!==comp && selectHasText(s,'Not included') &&\n              Array.from(s.options||[]).some(function(o){return /1000.*standard/i.test(cleanText(o.textContent||''));});\n          });\n          if(!coll){\n            coll=sels.find(function(s){return s!==comp && selectHasText(s,'$1,000')&&selectHasText(s,'Not included');});\n          }\n          if(!coll) coll=sels.find(function(s){return s!==comp;})||null;\n          if(coll){\n            const target=full?(selectHasText(coll,'$1,000 Standard')?'$1,000 Standard':'$1,000'):'Not included';\n            if(!await setVehicleSelect(coll,target)) all=false;\n          }else all=false;\n\n          const road=sels.find(function(s){return selectHasText(s,'Yes');}) || sels[2] || null;\n          if(road){ if(!await setVehicleSelect(road,'Yes')) all=false; } else all=false;\n        }else{\n          // Farmers collision uses a type selector (Regular / Not included) plus\n          // a deductible selector ($1,000). For older vehicles choose Not included.\n          const collType=sels.find(function(s){return s!==comp && selectHasText(s,'Regular');}) || sels[1] || null;\n          if(collType){\n            if(full){ if(!await setVehicleSelect(collType,'Regular')) all=false; }\n            else if(selectHasText(collType,'Not included')){ if(!await setVehicleSelect(collType,'Not included')) all=false; }\n          }else all=false;\n\n          if(full){\n            const typeIdx=collType?sels.indexOf(collType):-1;\n            const collDed=sels.find(function(s,idx){return s!==comp && idx>typeIdx && selectHasText(s,'$1,000');});\n            if(collDed){ if(!await setVehicleSelect(collDed,'$1,000')) all=false; } else all=false;\n          }\n\n          // Roadside assistance is always Standard for Farmers. Prefer a selector\n          // after the collision controls whose option text is exactly Standard.\n          const road=sels.slice().reverse().find(function(s){return selectHasText(s,'Standard');});\n          if(road){ if(!await setVehicleSelect(road,'Standard')) all=false; } else all=false;\n        }\n        await new Promise(function(resolve){setTimeout(resolve,60);});\n      }\n      return all;\n    }\n\n    let coveragePresetBusy=false;\n    let liabilityPresetDoneSig='';\n    let vehiclePresetDoneSig='';\n    async function applyAltaCoverageDefaults(){\n      if(!onAutoCoverageSection() || coveragePresetBusy) return;\n      const ident=altaIdentity();\n      const sig=(ident.id||'')+'|'+location.pathname;\n      if(sig===liabilityPresetDoneSig && sig===vehiclePresetDoneSig) return;\n      coveragePresetBusy=true;\n      try{\n        // Liability fields are applied only once per lead/page. Previously they\n        // were being selected again every 500 ms while vehicle cards were still\n        // loading, which caused visible repeated dropdown changes.\n        if(sig!==liabilityPresetDoneSig){\n          const preset=isBwCoveragePage()?BW_COVERAGE_PRESET:FARMERS_COVERAGE_PRESET;\n          let liabilityDone=true;\n          for(const pair of preset){\n            const label=pair[0],wanted=pair[1];\n            const sel=coverageSelectForLabel(label);\n            let ok=false;\n            if(sel){\n              const opt=optionForValue(sel,wanted);\n              if(opt && covNorm(sel.options[sel.selectedIndex]&&sel.options[sel.selectedIndex].textContent)===covNorm(wanted)) ok=true;\n              else ok=setCoverageNative(sel,wanted);\n            }\n            if(!ok) ok=await setCoverageFallback(label,wanted);\n            if(!ok) liabilityDone=false;\n            await new Promise(function(resolve){setTimeout(resolve,80);});\n          }\n          if(liabilityDone){\n            liabilityPresetDoneSig=sig;\n            console.log('[TritoX TM] ALTA liability preset applied once:',isBwCoveragePage()?'BW':'Farmers');\n          }\n        }\n\n        // Vehicle cards can render later than liability fields, so retry ONLY\n        // the vehicle defaults until every visible vehicle card is ready.\n        if(sig!==vehiclePresetDoneSig){\n          const vehiclesDone=await applyVehicleCoverageDefaults();\n          if(vehiclesDone){\n            vehiclePresetDoneSig=sig;\n            console.log('[TritoX TM] ALTA vehicle coverage preset applied once:',isBwCoveragePage()?'BW':'Farmers');\n          }\n        }\n      }finally{\n        coveragePresetBusy=false;\n      }\n    }\n\n    function detectStar(){\n      // Rating is detected ONLY on ALTA's Auto coverages page.\n      if(!onAutoCoverageSection()) return '';\n\n      // IMPORTANT: resolve Bristol West from the route BEFORE scanning text.\n      // The BW page can contain numbers such as Opt. 1 / Opt. 3 and other\n      // content that must never be interpreted as a Farmers star rating.\n      if(isBwCoveragePage()) return 'BW';\n\n      const text=document.body ? document.body.innerText : '';\n\n      // Farmers rating banner: 1 Star / 2 Stars / 3 Stars.\n      const m=text.match(/\\b([123])\\s*Stars?\\b/i);\n      if(m) return m[1];\n\n      // Bristol West coverage layout does not show a Star badge. In the BW\n      // layout ALTA shows the Bristol West PIP deductible row without the\n      // separate Farmers PIP deductible row that is present on Farmers quotes.\n      const hasBWPip=/Bristol\\s+West\\s+PIP\\s+deductible/i.test(text);\n      const hasFarmersPip=/Farmers\\s+PIP\\s+deductible/i.test(text);\n      if(hasBWPip && !hasFarmersPip) return 'BW';\n\n      // Extra safety: inspect the visible quote banner/logo for Bristol West\n      // branding. This helps if ALTA changes the PIP labels later.\n      const brandEls=Array.from(document.querySelectorAll('img,[aria-label],[title],[data-testid],[class],[id]')).filter(function(el){\n        try{\n          const r=el.getBoundingClientRect();\n          return r.width>0 && r.height>0 && r.top<260 && r.bottom>0;\n        }catch(e){ return false; }\n      });\n      const bwBrand=brandEls.some(function(el){\n        const bits=[\n          el.getAttribute&&el.getAttribute('alt'),\n          el.getAttribute&&el.getAttribute('title'),\n          el.getAttribute&&el.getAttribute('aria-label'),\n          el.getAttribute&&el.getAttribute('data-testid'),\n          el.id, el.className,\n          el.getAttribute&&el.getAttribute('src'),\n          el.style&&el.style.backgroundImage\n        ].map(function(v){return String(v||'');}).join(' ');\n        return /bristol\\s*west|bristolwest|(?:^|[^a-z])bw(?:[^a-z]|$)/i.test(bits);\n      });\n      if(bwBrand) return 'BW';\n\n      return '';\n    }\n\n    function storageKey(id){ return 'tritox_alta_lead_'+String(id||'unknown'); }\n    const ALTA_INDEX_KEY='tritox_alta_index';\n\n    function readAltaIndex(){\n      try{\n        const raw=GM_getValue(ALTA_INDEX_KEY,'{}');\n        const index=JSON.parse(raw||'{}')||{};\n        let changed=false;\n        Object.keys(index).forEach(function(id){\n          const m=index[id]||{};\n          if(!m._savedAt || Date.now()-Number(m._savedAt)>ALTA_META_TTL){\n            delete index[id];\n            changed=true;\n          }\n        });\n        if(changed) GM_setValue(ALTA_INDEX_KEY,JSON.stringify(index));\n        return index;\n      }catch(e){ return {}; }\n    }\n\n    function readSaved(id){\n      try{\n        const index=readAltaIndex();\n        if(index[id]) return index[id];\n        const obj=JSON.parse(GM_getValue(storageKey(id),'{}'))||{};\n        if(obj._savedAt && Date.now()-obj._savedAt>ALTA_META_TTL) return {};\n        return obj;\n      }catch(e){return {};}\n    }\n\n    function saveMeta(meta){\n      if(!meta || !meta.altaId) return;\n      meta._savedAt=Date.now();\n\n      // Keep every lead separately so 4-6 quotes can be worked in parallel.\n      // Nothing is overwritten just because another ALTA lead becomes current.\n      const index=readAltaIndex();\n      index[String(meta.altaId)]=meta;\n      GM_setValue(ALTA_INDEX_KEY,JSON.stringify(index));\n\n      // Per-ID key retained for backwards compatibility/debugging.\n      GM_setValue(storageKey(meta.altaId),JSON.stringify(meta));\n      GM_setValue('tritox_alta_latest',JSON.stringify(meta));\n      console.log('[TritoX TM] ALTA metadata auto-saved:',meta,'cached leads:',Object.keys(index).length);\n    }\n\n    function buildPanel(){\n      const ident=altaIdentity();\n      if(!ident.id || !ident.name) return;\n      let panel=document.getElementById('tritox-alta-panel');\n      const existing=readSaved(ident.id);\n      const prior=priorInsuranceSnapshot();\n      const detected={\n        company:prior.inEffect?prior.company:'',\n        renewalDate:prior.inEffect?prior.renewalDate:'',\n        star:detectStar()\n      };\n      // If the Prior insurance section is visible and is not In Effect, clear\n      // carrier/date for this lead. On other ALTA pages preserve the last valid\n      // In Effect values captured earlier in the same 2-hour window.\n      if(prior.visible && !prior.inEffect){\n        existing.company='';\n        existing.renewalDate='';\n        existing.companyInEffect=false;\n      }\n      const state={\n        name:ident.name,\n        altaId:ident.id,\n        company:(prior.inEffect?detected.company:(existing.companyInEffect?existing.company:''))||'',\n        renewalDate:(prior.inEffect?detected.renewalDate:(existing.companyInEffect?existing.renewalDate:''))||'',\n        // Keep blank until ALTA's Auto coverages page exposes the actual star.\n        // On that page, the detected value wins so an old/manual value cannot\n        // survive when ALTA clearly shows a different rating.\n        star:onAutoCoverageSection() ? (detected.star||'') : (existing.star||''),\n        companyInEffect:prior.inEffect?true:!!existing.companyInEffect\n      };\n\n      if(!panel){\n        panel=document.createElement('div');\n        panel.id='tritox-alta-panel';\n        panel.style.cssText='position:fixed;right:20px;bottom:20px;z-index:2147483646;width:315px;background:#fff;border:2px solid #17243b;border-radius:12px;box-shadow:0 8px 28px rgba(0,0,0,.18);padding:14px;font-family:Arial,sans-serif;color:#17243b;';\n        panel.innerHTML=''\n          +'<div style=\"display:flex;justify-content:space-between;align-items:center;margin-bottom:10px;\">'\n          +'<strong style=\"font-size:14px;\">TritoX Lead Info</strong><span id=\"tx-alta-status\" style=\"font-size:11px;color:#64748b;\">Auto-saved</span></div>'\n          +'<div id=\"tx-alta-ident\" style=\"font-size:11px;color:#64748b;margin-bottom:10px;line-height:1.45;\"></div>'\n          +'<label style=\"display:block;font-size:11px;font-weight:700;margin:7px 0 4px;\">Current Company</label>'\n          +'<input id=\"tx-alta-company\" list=\"tx-carriers\" placeholder=\"Select or type carrier\" style=\"width:100%;height:34px;border:1px solid #cbd5e1;border-radius:7px;padding:0 9px;font-size:12px;\">'\n          +'<datalist id=\"tx-carriers\">'+CARRIERS.map(function(c){return '<option value=\"'+c.replace(/\"/g,'&quot;')+'\"></option>';}).join('')+'</datalist>'\n          +'<label style=\"display:block;font-size:11px;font-weight:700;margin:9px 0 4px;\">Auto Renewal Date</label>'\n          +'<input id=\"tx-alta-date\" type=\"text\" placeholder=\"MM/DD/YYYY\" style=\"width:100%;height:34px;border:1px solid #cbd5e1;border-radius:7px;padding:0 9px;font-size:12px;\">'\n          +'<label style=\"display:block;font-size:11px;font-weight:700;margin:9px 0 4px;\">Star / BW</label>'\n          +'<select id=\"tx-alta-star\" style=\"width:100%;height:34px;border:1px solid #cbd5e1;border-radius:7px;padding:0 8px;font-size:12px;background:#fff;\">'\n          +'<option value=\"\">Select</option><option value=\"1\">1 Star</option><option value=\"2\">2 Stars</option><option value=\"3\">3 Stars</option><option value=\"BW\">BW</option></select>'\n          +'<button id=\"tx-alta-save\" style=\"width:100%;margin-top:11px;height:36px;border:0;border-radius:8px;background:#2563eb;color:white;font-weight:700;cursor:pointer;\">Save Lead Info</button>';\n        document.body.appendChild(panel);\n\n        const saveNow=function(){\n          const now=altaIdentity();\n          if(!now.id) return;\n          const priorNow=priorInsuranceSnapshot();\n          const prev=readSaved(now.id);\n          const validInEffect=priorNow.visible ? priorNow.inEffect : !!prev.companyInEffect;\n          const meta={\n            name:now.name||state.name,\n            altaId:now.id,\n            company:validInEffect?document.getElementById('tx-alta-company').value.trim():'',\n            renewalDate:validInEffect?document.getElementById('tx-alta-date').value.trim():'',\n            star:document.getElementById('tx-alta-star').value,\n            companyInEffect:validInEffect\n          };\n          saveMeta(meta);\n          const s=document.getElementById('tx-alta-status');\n          if(s){s.textContent='Saved ✓';s.style.color='#15803d';setTimeout(function(){if(s){s.textContent='Auto-saved';s.style.color='#64748b';}},1200);}\n        };\n        document.getElementById('tx-alta-save').addEventListener('click',saveNow);\n        document.getElementById('tx-alta-company').addEventListener('change',saveNow);\n        document.getElementById('tx-alta-date').addEventListener('change',saveNow);\n        document.getElementById('tx-alta-star').addEventListener('change',saveNow);\n      }\n\n      document.getElementById('tx-alta-ident').textContent=ident.name+'  •  ALTA #'+ident.id;\n      const comp=document.getElementById('tx-alta-company');\n      const date=document.getElementById('tx-alta-date');\n      const star=document.getElementById('tx-alta-star');\n      if(comp && !comp.value && state.company) comp.value=state.company;\n      if(date && !date.value && state.renewalDate) date.value=state.renewalDate;\n      if(star){\n        if(isBwCoveragePage()){\n          // BW route is authoritative: force the panel to BW immediately.\n          star.value='BW';\n        }else if(onAutoCoverageSection()){\n          // Auto-detect when ALTA displays the rating; otherwise leave blank so\n          // the user can choose 1/2/3/BW manually.\n          star.value=state.star||'';\n        }else if(!star.value && state.star){\n          star.value=state.star;\n        }\n      }\n\n      // If ALTA exposes new values as the user moves to another quote page,\n      // merge them without overwriting a manual choice already made.\n      const current=readSaved(ident.id);\n      const merged={\n        name:ident.name, altaId:ident.id,\n        company:state.companyInEffect?(current.company||state.company||''):'',\n        renewalDate:state.companyInEffect?(current.renewalDate||state.renewalDate||''):'',\n        star:isBwCoveragePage() ? 'BW' : (onAutoCoverageSection() ? (state.star||'') : (current.star||state.star||'')),\n        companyInEffect:!!state.companyInEffect\n      };\n      if(merged.company || merged.renewalDate || merged.star || prior.visible) saveMeta(merged);\n    }\n\n    buildPanel();\n    applyAltaCoverageDefaults();\n    // ALTA is a SPA. Re-read the page as the quote moves between sections.\n    let lastSig='';\n    setInterval(function(){\n      const i=altaIdentity();\n      const sig=i.id+'|'+location.pathname+'|'+detectCarrier()+'|'+detectRenewalDate()+'|'+detectStar();\n      if(sig!==lastSig){\n        lastSig=sig;\n        liabilityPresetDoneSig='';\n        vehiclePresetDoneSig='';\n        buildPanel();\n      }\n      applyAltaCoverageDefaults();\n    },500);\n    return;\n  }\n  if(window.location.hostname === 'tritoxtech.github.io' || (window.location.hostname === 'saravanatritox-cloud.github.io' && window.location.pathname.startsWith('/aaron/'))){\n    console.log('[TritoX TM] Running on TritoX page');\n\n    // Aaron's current GitHub page omits the processed PDF filename from\n    // buildAZData(). Patch it at runtime so AgencyZoom can retrieve the exact\n    // cached PDF that belongs to the selected quote.\n    (function patchAaronFilenameTransfer(){\n      const pageWindow=typeof unsafeWindow!=='undefined'?unsafeWindow:window;\n      let attempts=0;\n      const timer=setInterval(function(){\n        attempts++;\n        const original=pageWindow.buildAZData;\n        if(typeof original==='function' && !original.__tritoxFilenamePatched){\n          const patched=function(result){\n            const data=original.apply(this,arguments);\n            if(data && result){\n              if(result.filename) data._filename=result.filename;\n              // Transfer QC routing flags so AgencyZoom can choose the correct tags.\n              data._highPrice=!!result.putInStop;\n              if(result.homeData) data._isBundle=!!result.homeData.isBundle;\n            }\n            return data;\n          };\n          patched.__tritoxFilenamePatched=true;\n          pageWindow.buildAZData=patched;\n          clearInterval(timer);\n          console.log('[TritoX TM] Aaron filename transfer patch installed');\n        }else if(original && original.__tritoxFilenamePatched){\n          clearInterval(timer);\n        }else if(attempts>=80){\n          clearInterval(timer);\n          console.warn('[TritoX TM] Aaron buildAZData was not found');\n        }\n      },250);\n    })();\n\n    function pdfStorageKey(name){\n      let h=2166136261;\n      const s=String(name||'quote.pdf').toLowerCase();\n      for(let i=0;i<s.length;i++){\n        h^=s.charCodeAt(i);\n        h=Math.imul(h,16777619);\n      }\n      return 'tritox_pdf_'+(h>>>0).toString(16);\n    }\n\n    function cachePdf(file){\n      if(!file || !/.pdf$/i.test(file.name)) return;\n      if(file.size>20*1024*1024){\n        console.warn('[TritoX TM] PDF is larger than 20 MB and was not cached:',file.name);\n        return;\n      }\n      const reader=new FileReader();\n      reader.onload=function(){\n        try{\n          GM_setValue(pdfStorageKey(file.name),JSON.stringify({\n            name:file.name,\n            type:file.type||'application/pdf',\n            size:file.size,\n            lastModified:file.lastModified||Date.now(),\n            dataUrl:String(reader.result),\n            savedAt:Date.now()\n          }));\n          console.log('[TritoX TM] PDF cached for AgencyZoom:',file.name);\n        }catch(err){\n          console.error('[TritoX TM] Could not cache PDF:',err);\n        }\n      };\n      reader.readAsDataURL(file);\n    }\n\n    // Capture PDFs selected or dropped into Aaron QC. Each file is stored under\n    // its filename so bulk processing can still match the correct customer PDF.\n    document.addEventListener('change',function(e){\n      if(e.target && e.target.id==='fileInput' && e.target.files){\n        Array.from(e.target.files).forEach(cachePdf);\n      }\n    },true);\n    document.addEventListener('drop',function(e){\n      if(e.dataTransfer && e.dataTransfer.files && e.target.closest && e.target.closest('#uploadZone')){\n        Array.from(e.dataTransfer.files).forEach(cachePdf);\n      }\n    },true);\n\n    function checkTritoXData(){\n      try{\n        const raw = localStorage.getItem('tritox_az_data');\n        if(!raw) return;\n        const data = JSON.parse(raw);\n        if(!data || !data._name || !data._ts) return;\n\n        // Find the correct ALTA metadata from the multi-lead cache.\n        // This supports processing 4-6 ALTA quotes first, then uploading PDFs\n        // later in any order without reopening or re-saving each lead.\n        try{\n          const norm=function(v){\n            return String(v||'').toLowerCase().replace(/[^a-z0-9]+/g,' ').trim().replace(/\\s+/g,' ');\n          };\n          const firstLast=function(v){\n            const p=norm(v).split(' ').filter(Boolean);\n            return p.length>=2 ? p[0]+' '+p[p.length-1] : p.join(' ');\n          };\n          const indexRaw=GM_getValue('tritox_alta_index','{}');\n          const index=JSON.parse(indexRaw||'{}')||{};\n          const now=Date.now();\n          const valid=Object.keys(index).map(function(k){return index[k];}).filter(function(a){\n            return a && a.name && a._savedAt && now-Number(a._savedAt)<=7200000;\n          });\n\n          let a=valid.find(function(x){return norm(x.name)===norm(data._name);})||null;\n\n          // If the PDF includes/omits a middle name or initial, use first+last\n          // only when exactly one cached lead matches that pair.\n          if(!a){\n            const key=firstLast(data._name);\n            const matches=valid.filter(function(x){return firstLast(x.name)===key;});\n            if(matches.length===1) a=matches[0];\n          }\n\n          // Backward-compatible fallback for data captured before v4.36.\n          if(!a){\n            const latestRaw=GM_getValue('tritox_alta_latest','');\n            if(latestRaw){\n              const latest=JSON.parse(latestRaw);\n              if(latest && latest.name && norm(latest.name)===norm(data._name)) a=latest;\n            }\n          }\n\n          if(a){\n            data.current_company=a.companyInEffect ? (a.company||'') : '';\n            data.auto_renewal_date=a.companyInEffect ? (a.renewalDate||'') : '';\n            data.star_rating=a.star||'';\n            data._altaId=a.altaId||'';\n            data._altaSavedAt=a._savedAt||0;\n            localStorage.setItem('tritox_az_data',JSON.stringify(data));\n            console.log('[TritoX TM] Multi-lead ALTA match:',data._name,'=>',a.name,a.altaId);\n          }else{\n            // Explicitly keep these blank rather than borrowing another lead's data.\n            data.current_company='';\n            data.auto_renewal_date='';\n            data.star_rating='';\n            data._altaId='';\n            data._altaSavedAt=0;\n            localStorage.setItem('tritox_az_data',JSON.stringify(data));\n            console.log('[TritoX TM] No ALTA cache match for:',data._name);\n          }\n        }catch(e){ console.warn('[TritoX TM] ALTA multi-lead merge skipped:',e); }\n\n        const gmRaw = GM_getValue('tritox_az_data','');\n        let gmData={};\n        try{ gmData = JSON.parse(gmRaw||'{}')||{}; }catch(e){}\n        const gmTs = Number(gmData._ts||0);\n        const metaChanged =\n          String(gmData.current_company||'')!==String(data.current_company||'') ||\n          String(gmData.auto_renewal_date||'')!==String(data.auto_renewal_date||'') ||\n          String(gmData.star_rating||'')!==String(data.star_rating||'') ||\n          String(gmData._altaId||'')!==String(data._altaId||'');\n        if(data._ts > gmTs || (data._ts===gmTs && metaChanged)){\n          GM_setValue('tritox_az_data', JSON.stringify(data));\n          console.log('[TritoX TM] Saved to GM for:', data._name, 'metadata changed:',metaChanged);\n        }\n      }catch(e){ console.log('[TritoX TM] Error:', e); }\n    }\n    setInterval(checkTritoXData, 1000);\n    return;\n  }\n\n  console.log('[TritoX TM] Running on AgencyZoom page');\n\n  function normalizeLeadName(value){\n    return String(value||'').toLowerCase().replace(/[^a-z0-9]+/g,' ').trim().replace(/\\s+/g,' ');\n  }\n\n  // v4.44: Merge ALTA metadata again at the exact moment Fill is clicked.\n  // This removes timing dependence on the QC bridge and is important when\n  // several ALTA leads are quoted first, then PDFs are processed later.\n  function mergeAltaMetadataAtFill(data){\n    try{\n      if(!data || !data._name) return data;\n      const indexRaw=GM_getValue('tritox_alta_index','{}');\n      const index=JSON.parse(indexRaw||'{}')||{};\n      const now=Date.now();\n      const valid=Object.keys(index).map(function(k){return index[k];}).filter(function(a){\n        return a && a.name && a._savedAt && now-Number(a._savedAt)<=7200000;\n      });\n      const target=normalizeLeadName(data._name);\n      let matches=valid.filter(function(a){return normalizeLeadName(a.name)===target;});\n\n      if(!matches.length){\n        const parts=target.split(' ').filter(Boolean);\n        const key=parts.length>=2 ? parts[0]+' '+parts[parts.length-1] : target;\n        const loose=valid.filter(function(a){\n          const p=normalizeLeadName(a.name).split(' ').filter(Boolean);\n          const k=p.length>=2 ? p[0]+' '+p[p.length-1] : p.join(' ');\n          return k===key;\n        });\n        if(loose.length===1) matches=loose;\n      }\n\n      if(matches.length){\n        matches.sort(function(a,b){return Number(b._savedAt||0)-Number(a._savedAt||0);});\n        const a=matches[0];\n        data.current_company=a.companyInEffect ? String(a.company||'') : '';\n        data.auto_renewal_date=a.companyInEffect ? String(a.renewalDate||'') : '';\n        data.star_rating=String(a.star||'');\n        data._altaId=String(a.altaId||'');\n        data._altaSavedAt=Number(a._savedAt||0);\n        console.log('[TritoX TM] Direct ALTA merge at Fill:',data._name,data.current_company,data.auto_renewal_date,data.star_rating,data._altaId);\n      }else{\n        console.log('[TritoX TM] No direct ALTA match at Fill for:',data._name);\n      }\n      return data;\n    }catch(e){\n      console.warn('[TritoX TM] Direct ALTA merge failed:',e);\n      return data;\n    }\n  }\n\n  function getVisibleLeadHeaderText(){\n    const pieces=[];\n    const selectors=[\n      '#referral-container',\n      '[class*=\\\"lead-header\\\"]','[class*=\\\"referral-header\\\"]','[class*=\\\"contact-header\\\"]',\n      '[class*=\\\"leadHeader\\\"]','[class*=\\\"referralHeader\\\"]','[class*=\\\"contactHeader\\\"]'\n    ];\n    selectors.forEach(function(selector){\n      document.querySelectorAll(selector).forEach(function(el){\n        const r=el.getBoundingClientRect();\n        if(r.width>0 && r.height>0 && r.top<220){\n          pieces.push(el.innerText||el.textContent||'');\n        }\n      });\n    });\n    // AgencyZoom's lead name is often outside #referral-container. Capture only\n    // visible text in the upper lead pane so old activity/history names do not count.\n    document.querySelectorAll('h1,h2,h3,h4,strong,b,span,div').forEach(function(el){\n      const r=el.getBoundingClientRect();\n      if(r.width<=0 || r.height<=0 || r.top<0 || r.top>150 || r.left<0) return;\n      const text=(el.textContent||'').trim();\n      if(text && text.length<=100) pieces.push(text);\n    });\n    return normalizeLeadName(pieces.join(' | '));\n  }\n\n  function currentLeadMatchesData(data){\n    const wanted=normalizeLeadName(data && data._name);\n    if(!wanted) return true;\n    const header=getVisibleLeadHeaderText();\n    return !!header && header.includes(wanted);\n  }\n\n  // Fill popup lifecycle: same 7-second untouched window used by the\n  // original Aaron/TritoX workflow. Once a popup expires/cancels, the same\n  // processed lead will not be recreated; any newer lead replaces it instantly.\n  let expiredTs = Number(GM_getValue('tritox_popup_expired_ts',0)) || 0;\n\n  function expireFillPopup(ts){\n    expiredTs=Math.max(expiredTs,Number(ts||0));\n    GM_setValue('tritox_popup_expired_ts',expiredTs);\n    const oldBtn=document.getElementById('tritox-fill-btn');\n    if(oldBtn) oldBtn.remove();\n    const oldOverlay=document.getElementById('tritox-fill-overlay');\n    if(oldOverlay) oldOverlay.remove();\n  }\n\n  function addFillButton(){\n    const raw = GM_getValue('tritox_az_data','');\n    if(!raw) return;\n    let data;\n    try{ data = JSON.parse(raw); }catch(e){ return; }\n    if(!data || !data._name) return;\n\n    const buttonDataTs = data._ts || 0;\n\n    // Do not recreate data that was already filled.\n    if(buttonDataTs <= filledTs || buttonDataTs <= expiredTs) return;\n\n    const age = Date.now() - buttonDataTs;\n    if(age > 7200000) return;\n\n    // If a new PDF/lead arrives while AgencyZoom stays on the same URL,\n    // replace the previous Fill button immediately.\n    const existingBtn=document.getElementById('tritox-fill-btn');\n    if(existingBtn){\n      const existingTs=Number(existingBtn.dataset.tritoxTs||0);\n      if(existingTs===buttonDataTs) return;\n      existingBtn.remove();\n    }\n\n    const btn = document.createElement('div');\n    btn.dataset.tritoxTs=String(buttonDataTs);\n    btn.id = 'tritox-fill-btn';\n    btn.style.cssText = 'position:fixed;top:80px;right:20px;z-index:99999;background:linear-gradient(135deg,#00d4ff,#7b2fff);color:#fff;padding:10px 16px;border-radius:10px;cursor:pointer;font-size:13px;font-weight:700;box-shadow:0 4px 20px rgba(0,212,255,0.4);font-family:sans-serif;text-align:center;min-width:160px;';\n    btn.innerHTML = '🚀 Fill + Attach PDF<br><span style=\"font-size:11px;font-weight:400;opacity:0.9;\">' + data._name + '</span>';\n\n    // Initial Fill button is visible for a maximum of 7 seconds if untouched.\n    let autoHideTimer=null;\n\n    btn.addEventListener('click', function(){\n      if(autoHideTimer){ clearTimeout(autoHideTimer); autoHideTimer=null; }\n      // Show confirmation popup\n      const oldOverlay=document.getElementById('tritox-fill-overlay');\n      if(oldOverlay) oldOverlay.remove();\n      const overlay = document.createElement('div');\n      overlay.id='tritox-fill-overlay';\n      overlay.dataset.tritoxTs=String(buttonDataTs);\n      overlay.style.cssText = 'position:fixed;inset:0;background:rgba(0,0,0,0.7);z-index:999999;display:flex;align-items:center;justify-content:center;';\n      const box = document.createElement('div');\n      box.style.cssText = 'background:#fff;border-radius:16px;padding:28px 32px;max-width:380px;width:90%;text-align:center;font-family:sans-serif;box-shadow:0 20px 60px rgba(0,0,0,0.3);';\n      box.innerHTML = '<div style=\"font-size:32px;margin-bottom:12px;\">⚠️</div>'\n        +'<div style=\"font-size:16px;font-weight:700;color:#1a1a2e;margin-bottom:8px;\">Confirm Fill + PDF Attachment</div>'\n        +'<div style=\"font-size:13px;color:#666;margin-bottom:6px;\">You are about to fill and attach the quote PDF for:</div>'\n        +'<div style=\"font-size:15px;font-weight:700;color:#7b2fff;margin-bottom:20px;padding:10px;background:#f0e8ff;border-radius:8px;\">'+data._name+'</div>'\n        +'<div style=\"font-size:12px;color:#999;margin-bottom:20px;\">Make sure you are on the correct lead in AgencyZoom before confirming.</div>'\n        +'<div style=\"display:flex;gap:10px;justify-content:center;\">'\n        +'<button id=\"tritox-cancel\" style=\"flex:1;padding:10px;border:2px solid #ddd;background:#fff;border-radius:8px;cursor:pointer;font-size:13px;font-weight:600;color:#666;\">❌ Cancel</button>'\n        +'<button id=\"tritox-confirm\" style=\"flex:1;padding:10px;border:none;background:linear-gradient(135deg,#00d4ff,#7b2fff);border-radius:8px;cursor:pointer;font-size:13px;font-weight:700;color:#fff;\">✅ Fill + Attach</button>'\n        +'</div>';\n      overlay.appendChild(box);\n      document.body.appendChild(overlay);\n\n      // Cancel\n      document.getElementById('tritox-cancel').addEventListener('click', function(){\n        overlay.remove();\n        expireFillPopup(buttonDataTs);\n      });\n\n      // Confirm\n      document.getElementById('tritox-confirm').addEventListener('click', async function(){\n        const tritoxStart=performance.now();\n        overlay.remove();\n        btn.style.background='linear-gradient(135deg,#0085ff,#7b2fff)';\n        btn.innerHTML='⏳ Attaching PDF…<br><span style=\"font-size:11px;font-weight:400;opacity:0.9;\">'+data._name+'</span>';\n\n        // v4.22 ULTRAFAST: keep the lead on Main, fill immediately, and run\n        // PDF upload + tag save in parallel. This removes the slow Files-tab\n        // verification/repaint cycle that previously consumed most of the time.\n        if(!document.getElementById('customfields-cf30203')){\n          await openLeadTab('Main');\n          await waitFor(function(){return document.getElementById('customfields-cf30203');},800);\n        }\n        // Always refresh the correct ALTA record immediately before filling.\n        // This makes company/date/Star-BW work even when QC was processed before\n        // the latest ALTA auto-save or when several leads are handled in a batch.\n        mergeAltaMetadataAtFill(data);\n        fillFields(data);\n\n        // Start PDF upload immediately. Give AgencyZoom a very short moment to\n        // commit the Main-page custom-field/select changes before opening Add Tag.\n        // This prevents the Star field/selectpicker update from racing the tag modal.\n        const pdfPromise=attachPdfFast(data);\n        await wait(180);\n        let tagResult=await applyQuoteTags(data);\n        if(!tagResult.ok){\n          // One fast retry handles transient AgencyZoom re-renders without\n          // changing the user's working field/PDF flow.\n          await wait(140);\n          tagResult=await applyQuoteTags(data);\n        }\n        const attachResult=await pdfPromise;\n        console.log('[TritoX TM] Total automation time:', Math.round(performance.now()-tritoxStart)+'ms', {pdf:attachResult, tags:tagResult});\n        // Store timestamp of this fill to prevent reappearing\n        try{\n          const d = JSON.parse(GM_getValue('tritox_az_data','{}'));\n          filledTs = d._ts || Date.now();\n        }catch(e){ filledTs = Date.now(); }\n        GM_setValue('tritox_az_data','');\n        if(attachResult.ok && data._filename){\n          GM_deleteValue(pdfStorageKey(data._filename));\n        }\n\n        // Show done state with close button and countdown\n        let secs = 1;\n        btn.style.background = attachResult.ok && tagResult.ok ? 'linear-gradient(135deg,#00e887,#00b359)' : '#a65b00';\n        btn.style.minWidth = '180px';\n\n        function updateBtn(){\n          btn.innerHTML = (attachResult.ok && tagResult.ok?'✅ Filled + Verified PDF + Tags':'⚠️ Filled — Review PDF / Tags')+'<br>'\n            +'<span style=\"font-size:11px;font-weight:400;opacity:0.9;\">' + data._name + '</span><br>'\n            +'<span style=\"font-size:10px;font-weight:400;opacity:0.9;\">'+attachResult.message+'</span><br>'\n            +'<span style=\"font-size:10px;font-weight:400;opacity:0.9;\">'+tagResult.message+'</span><br>'\n            +'<div style=\"display:flex;align-items:center;justify-content:center;gap:8px;margin-top:6px;\">'\n            +'<span style=\"font-size:10px;opacity:0.8;\">Auto close in '+secs+'s</span>'\n            +'<button id=\"tritox-close-btn\" style=\"background:rgba(255,255,255,0.25);border:1px solid rgba(255,255,255,0.5);color:#fff;border-radius:6px;padding:2px 8px;cursor:pointer;font-size:11px;font-weight:700;\">✕ Close</button>'\n            +'</div>';\n\n          // Attach close button listener after innerHTML update\n          const closeBtn = document.getElementById('tritox-close-btn');\n          if(closeBtn){\n            closeBtn.addEventListener('click', function(e){\n              e.stopPropagation();\n              btn.remove();\n            });\n          }\n        }\n\n        updateBtn();\n\n        // Countdown timer\n        const timer = setInterval(function(){\n          secs--;\n          if(secs <= 0){\n            clearInterval(timer);\n            btn.remove();\n          } else {\n            updateBtn();\n          }\n        }, 400);\n      });\n\n      // Click outside to cancel\n      overlay.addEventListener('click', function(e){\n        if(e.target === overlay){\n          overlay.remove();\n          expireFillPopup(buttonDataTs);\n        }\n      });\n    });\n\n    document.body.appendChild(btn);\n\n    // If untouched, disappear after 7 seconds and do not recreate this same\n    // lead. Processing a new PDF creates a newer timestamp and shows immediately.\n    autoHideTimer=setTimeout(function(){\n      if(btn.isConnected) btn.remove();\n      expiredTs=Math.max(expiredTs,buttonDataTs);\n      GM_setValue('tritox_popup_expired_ts',expiredTs);\n    },7000);\n\n  }\n\n  function pdfStorageKey(name){\n    let h=2166136261;\n    const s=String(name||'quote.pdf').toLowerCase();\n    for(let i=0;i<s.length;i++){\n      h^=s.charCodeAt(i);\n      h=Math.imul(h,16777619);\n    }\n    return 'tritox_pdf_'+(h>>>0).toString(16);\n  }\n\n  function wait(ms){return new Promise(function(resolve){setTimeout(resolve,ms);});}\n\n  async function waitFor(getter,timeout){\n    const start=Date.now();\n    while(Date.now()-start<timeout){\n      const value=getter();\n      if(value) return value;\n      await wait(250);\n    }\n    return null;\n  }\n\n  function findLeadTab(label){\n    const wanted=String(label).toLowerCase();\n    const match=Array.from(document.querySelectorAll('#referral-container a,#referral-container button,#referral-container [role=\"tab\"],#referral-container li,#referral-container span,#referral-container div'))\n      .find(function(el){return (el.textContent||'').trim().toLowerCase()===wanted;})||null;\n    return match ? (match.closest('a,button,li,[role=\"tab\"]')||match) : null;\n  }\n\n  async function openLeadTab(label){\n    const tab=findLeadTab(label);\n    if(!tab) return false;\n    tab.click();\n    await wait(900);\n    return true;\n  }\n\n  function payloadToFile(payload){\n    const parts=String(payload.dataUrl||'').split(',');\n    if(parts.length<2) throw new Error('Stored PDF data is incomplete');\n    const bytes=atob(parts[1]);\n    const array=new Uint8Array(bytes.length);\n    for(let i=0;i<bytes.length;i++) array[i]=bytes.charCodeAt(i);\n    return new File([array],payload.name,{\n      type:payload.type||'application/pdf',\n      lastModified:payload.lastModified||Date.now()\n    });\n  }\n\n  function findAgencyZoomFileInput(){\n    const selectors=[\n      '#referral-container input[type=\"file\"]',\n      '.agencydocupload_doc input[type=\"file\"]',\n      '#agencyDocUploader input[type=\"file\"]',\n      'input.agencydocupload_doc[type=\"file\"]',\n      'input[type=\"file\"][multiple]',\n      'input[type=\"file\"]'\n    ];\n    for(const selector of selectors){\n      const inputs=Array.from(document.querySelectorAll(selector));\n      if(inputs.length) return inputs[inputs.length-1];\n    }\n    return null;\n  }\n\n  function pdfNameIsVisible(fileName){\n    const wanted=String(fileName||'').toLowerCase();\n    const panel=document.getElementById('referral-container');\n    if(!panel || !wanted) return false;\n    if((panel.innerText||'').toLowerCase().includes(wanted)) return true;\n    return Array.from(panel.querySelectorAll('[title],[data-name],[data-file-name],a'))\n      .some(function(el){\n        return [el.getAttribute('title'),el.getAttribute('data-name'),el.getAttribute('data-file-name'),el.textContent]\n          .some(function(value){return String(value||'').toLowerCase().includes(wanted);});\n      });\n  }\n\n  function getCurrentLeadId(){\n    // The visible \"ID: 12345678\" is in AgencyZoom's lead header, outside\n    // #referral-container. Search the complete rendered page first.\n    const texts=[\n      document.body&&document.body.innerText,\n      document.documentElement&&document.documentElement.innerText,\n      document.getElementById('referral-container')&&document.getElementById('referral-container').innerText\n    ];\n    for(const text of texts){\n      const match=String(text||'').match(/\\bID\\s*:\\s*(\\d{5,})\\b/i);\n      if(match) return match[1];\n    }\n\n    const selectors=[\n      '[data-entity-id]','[data-entityid]','[data-lead-id]','[data-leadid]',\n      'input[name=\"entityId\"]','input[name=\"leadId\"]'\n    ];\n    for(const selector of selectors){\n      const el=document.querySelector(selector);\n      if(!el) continue;\n      const value=el.value||el.getAttribute('data-entity-id')||el.getAttribute('data-entityid')||\n        el.getAttribute('data-lead-id')||el.getAttribute('data-leadid');\n      if(/^\\d{5,}$/.test(String(value||''))) return String(value);\n    }\n\n    // Final fallback: AgencyZoom often embeds the active lead ID in its\n    // uploader configuration even when the header has not finished rendering.\n    const html=document.documentElement&&document.documentElement.innerHTML||'';\n    const configMatch=html.match(/(?:entityId|leadId)[\"']?\\s*[:=]\\s*[\"']?(\\d{5,})/i);\n    if(configMatch) return configMatch[1];\n    return '';\n  }\n\n  async function uploadPdfDirect(file,leadId){\n    const pageWindow=typeof unsafeWindow!=='undefined'?unsafeWindow:window;\n    const jq=pageWindow.jQuery;\n    if(!jq || typeof jq.ajax!=='function') throw new Error('AgencyZoom uploader session was not ready');\n\n    const query=new URLSearchParams({\n      docType:'undefined',\n      fileName:file.name,\n      docuSign:'0'\n    });\n    // Create the file and FormData in AgencyZoom's own page context. This lets\n    // its jQuery AJAX configuration apply the same session/security handling\n    // used by the successful manual uploader.\n    const bytes=await file.arrayBuffer();\n    const pageFile=new pageWindow.File([bytes],file.name,{\n      type:file.type||'application/pdf',\n      lastModified:file.lastModified||Date.now()\n    });\n    const form=new pageWindow.FormData();\n    form.append('contacts','[]');\n    form.append('emailSubject','');\n    form.append('emailBody','');\n    form.append('entityId',String(leadId));\n    form.append('linkToType','lead');\n    form.append('files[]',pageFile,pageFile.name);\n\n    return new Promise(function(resolve,reject){\n      jq.ajax({\n        url:'/lead/doc?'+query.toString(),\n        type:'POST',\n        data:form,\n        processData:false,\n        contentType:false,\n        cache:false,\n        success:function(result){\n          if(result && result.docName && String(result.docName)!==file.name){\n            reject(new Error('AgencyZoom returned a different filename'));\n            return;\n          }\n          resolve(result);\n        },\n        error:function(xhr){\n          let detail='HTTP '+(xhr&&xhr.status||'error');\n          try{\n            const body=xhr.responseJSON||JSON.parse(xhr.responseText||'{}');\n            detail=body.message||body.error||detail;\n          }catch(e){}\n          reject(new Error(detail));\n        }\n      });\n    });\n  }\n\n  async function attachPdfFast(data){\n    if(!data._filename) return {ok:false,message:'PDF filename was not transferred'};\n    const stored=GM_getValue(pdfStorageKey(data._filename),'');\n    if(!stored) return {ok:false,message:'PDF was not cached — select it again in Aaron QC'};\n\n    let payload;\n    try{payload=JSON.parse(stored);}catch(e){return {ok:false,message:'Stored PDF could not be read'};}\n    if(payload.name!==data._filename) return {ok:false,message:'PDF filename mismatch — attachment stopped'};\n\n    const file=payloadToFile(payload);\n    const leadId=getCurrentLeadId();\n    if(!leadId) return {ok:false,message:'AgencyZoom lead ID was not found'};\n\n    // Fast mode: trust AgencyZoom's successful upload response instead of\n    // navigating to Files and waiting for the filename to repaint.\n    try{\n      const result=await uploadPdfDirect(file,leadId);\n      if(result===undefined || result===null) return {ok:true,message:'PDF upload accepted by AgencyZoom'};\n      return {ok:true,message:'PDF uploaded to AgencyZoom'};\n    }catch(err){\n      console.error('[TritoX TM] Fast PDF upload failed:',err);\n      return {ok:false,message:'AgencyZoom rejected PDF upload: '+String(err.message||err)};\n    }\n  }\n\n  async function attachPdfToLead(data){\n    if(!data._filename) return {ok:false,message:'PDF filename was not transferred'};\n    const stored=GM_getValue(pdfStorageKey(data._filename),'');\n    if(!stored) return {ok:false,message:'PDF was not cached — select it again in Aaron QC'};\n\n    let payload;\n    try{payload=JSON.parse(stored);}catch(e){return {ok:false,message:'Stored PDF could not be read'};}\n    if(payload.name!==data._filename) return {ok:false,message:'PDF filename mismatch — attachment stopped'};\n\n    const file=payloadToFile(payload);\n    const leadId=getCurrentLeadId();\n    if(!leadId) return {ok:false,message:'AgencyZoom lead ID was not found'};\n\n    // Upload through AgencyZoom's own multipart endpoint.\n    await openLeadTab('Files');\n    await waitFor(function(){return document.getElementById('referral-container');},5000);\n\n    try{\n      await uploadPdfDirect(file,leadId);\n    }catch(err){\n      console.error('[TritoX TM] Direct PDF upload failed:',err);\n      return {ok:false,message:'AgencyZoom rejected PDF upload: '+String(err.message||err)};\n    }\n\n    // IMPORTANT: a 200/upload object is not treated as success by itself.\n    // Confirm the actual filename appears in the lead's Files UI.\n    let visible=await waitFor(function(){\n      return pdfNameIsVisible(file.name) ? true : null;\n    },5000);\n\n    // AgencyZoom sometimes does not repaint the Files list immediately.\n    // Force a tab re-render once, then verify again.\n    if(!visible){\n      await openLeadTab('Main');\n      await wait(500);\n      await openLeadTab('Files');\n      visible=await waitFor(function(){\n        return pdfNameIsVisible(file.name) ? true : null;\n      },7000);\n    }\n\n    if(visible){\n      return {ok:true,message:'PDF verified in AgencyZoom Files'};\n    }\n\n    console.warn('[TritoX TM] Upload response received but file was not visible:',file.name);\n    return {\n      ok:false,\n      message:'PDF upload was not verified in Files — please check/attach manually'\n    };\n  }\n\n  function fillText(id, val){\n    if(!val && val !== 0) return;\n    const el = document.getElementById('customfields-' + id);\n    if(!el) return;\n    try{\n      el.focus();\n      const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set;\n      setter.call(el, String(val));\n      el.dispatchEvent(new Event('focus',{bubbles:true}));\n      el.dispatchEvent(new Event('input',{bubbles:true}));\n      el.dispatchEvent(new Event('change',{bubbles:true}));\n      el.dispatchEvent(new KeyboardEvent('keydown',{bubbles:true}));\n      el.dispatchEvent(new KeyboardEvent('keyup',{bubbles:true}));\n      el.blur();\n      el.dispatchEvent(new Event('blur',{bubbles:true}));\n    }catch(e){}\n  }\n\n  function fillSelect(id, val){\n    if(!val) return;\n    const el = document.getElementById('customfields-' + id);\n    if(!el) return;\n    try{\n      const setter = Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,'value').set;\n      setter.call(el, String(val));\n      el.dispatchEvent(new Event('change',{bubbles:true}));\n    }catch(e){}\n  }\n\n  function quoteTagNames(data){\n    const isBundle = typeof data._isBundle === 'boolean'\n      ? data._isBundle\n      : !!(data.home_coverage_a || data.home_annual);\n    const isHighPrice = !!(data && (data._highPrice === true || data.high_price === true || String(data._highPrice||'').toLowerCase()==='true'));\n    const names=[isHighPrice ? 'PRICE TOO HIGH' : 'Ready to send'];\n    if(isBundle) names.push('Home is quoted');\n    return names;\n  }\n\n  function resolveStarTagName(select,data){\n    const raw=String((data&&data.star_rating)||'').trim();\n    if(!raw || !select) return '';\n    const options=Array.from(select.options||[]).filter(function(o){return !o.disabled;});\n    const text=function(o){return String(o.textContent||'').replace(/\\s+/g,' ').trim();};\n    const starMatch=raw.match(/(?:^|\\b)([123])(?:\\s*stars?)?(?:\\b|$)/i);\n    const isBW=/\\bbw\\b|bristol\\s*west/i.test(raw);\n\n    if(starMatch){\n      const n=starMatch[1];\n      const patterns=[\n        new RegExp('^'+n+'\\\\s*stars?$', 'i'),\n        new RegExp('^star\\\\s*'+n+'$', 'i'),\n        new RegExp('^'+n+'$', 'i')\n      ];\n      for(const re of patterns){\n        const hit=options.find(function(o){return re.test(text(o));});\n        if(hit) return text(hit);\n      }\n    }\n    if(isBW){\n      const hit=options.find(function(o){return /^(?:BW|Bristol\\s*West)$/i.test(text(o));});\n      if(hit) return text(hit);\n    }\n    return '';\n  }\n\n  async function applyQuoteTags(data){\n    let toggle=null;\n    let opened=false;\n    try{\n      const names=quoteTagNames(data);\n      const norm=function(v){return String(v||'').replace(/\\s+/g,' ').trim().toLowerCase();};\n      const wanted=names.map(norm);\n      const pageWindow=typeof unsafeWindow!=='undefined'?unsafeWindow:window;\n      const jq=pageWindow.jQuery;\n      const PageEvent=pageWindow.Event||Event;\n\n      function isVisible(el){\n        if(!el) return false;\n        const r=el.getBoundingClientRect();\n        const cs=pageWindow.getComputedStyle? pageWindow.getComputedStyle(el):window.getComputedStyle(el);\n        return !!(el.getClientRects().length && r.width>0 && r.height>0 && cs.display!=='none' && cs.visibility!=='hidden');\n      }\n\n      function openAddTagPanelIfNeeded(){\n        // If the Add Tag panel is closed, open it first. AgencyZoom has used\n        // different button markup across builds, so match accessible labels,\n        // titles and nearby tag-related controls rather than one brittle selector.\n        const selectors=[\n          '[aria-label*=\"tag\" i]','[title*=\"tag\" i]','[data-original-title*=\"tag\" i]',\n          'button','a','[role=\"button\"]'\n        ];\n        const seen=new Set();\n        const candidates=[];\n        selectors.forEach(function(sel){\n          document.querySelectorAll(sel).forEach(function(el){\n            if(seen.has(el) || !isVisible(el)) return;\n            seen.add(el);\n            const txt=norm((el.getAttribute('aria-label')||'')+' '+(el.getAttribute('title')||'')+' '+(el.getAttribute('data-original-title')||'')+' '+(el.textContent||''));\n            if(txt==='add tag' || txt.includes('add tag') || txt==='tags' || txt==='tag') candidates.push(el);\n          });\n        });\n        if(candidates.length){\n          try{ candidates[0].click(); return true; }catch(e){}\n        }\n        return false;\n      }\n\n      function collectTagCandidates(){\n        return Array.from(document.querySelectorAll('select')).map(function(select,index){\n        const options=Array.from(select.options||[]);\n        const optionNames=options.map(function(o){return norm(o.textContent);});\n        if(!wanted.every(function(name){return optionNames.includes(name);})) return null;\n        const wrapper=select.closest('.bootstrap-select');\n        const button=wrapper&&wrapper.querySelector('button.dropdown-toggle');\n        const selectedNames=Array.from(select.selectedOptions||[]).map(function(o){return norm(o.textContent);}).filter(Boolean);\n        const identity=norm((select.id||'')+' '+(select.name||'')+' '+(select.className||''));\n        let score=0;\n        if(select.closest('#referral-container')) score+=1000;\n        if(select.name==='tags[]') score+=1000;\n        if(identity.includes('tag')) score+=600;\n        if(select.multiple) score+=400;\n        if(isVisible(wrapper||select)) score+=700;\n        if(button && isVisible(button)) score+=400;\n        if(selectedNames.length) score+=500;\n        if(selectedNames.includes('quote team')) score+=2000;\n        score+=index/10000;\n        return {select,wrapper,button,selectedNames,score};\n        }).filter(Boolean).sort(function(a,b){return b.score-a.score;});\n      }\n\n      let candidates=collectTagCandidates();\n      if(!candidates.length){\n        openAddTagPanelIfNeeded();\n        await wait(120);\n        candidates=collectTagCandidates();\n      }\n\n      if(!candidates.length) throw new Error('Lead tag field was not found — open Add Tag and add '+names.join(' + ')+' manually');\n      const chosen=candidates[0];\n      const select=chosen.select;\n      const wrapper=chosen.wrapper;\n      toggle=chosen.button;\n      if(!wrapper || !toggle) throw new Error('Lead tag dropdown UI was not found');\n\n      // Add the matching Star/BW TAG using the exact option text available in\n      // AgencyZoom. This is separate from filling the Star 1-3 custom field.\n      const starTagName=resolveStarTagName(select,data);\n      if(starTagName && !names.some(function(n){return norm(n)===norm(starTagName);})) names.push(starTagName);\n\n      const beforeValues=Array.from(select.selectedOptions||[]).map(function(o){return String(o.value);});\n\n      function syncTagSelect(changedIndex){\n        try{ select.dispatchEvent(new PageEvent('input',{bubbles:true})); }catch(e){}\n        try{ select.dispatchEvent(new PageEvent('change',{bubbles:true})); }catch(e){}\n        if(jq){\n          try{ jq(select).trigger('change'); }catch(e){}\n          if(Number.isInteger(changedIndex)){\n            try{ jq(select).trigger('changed.bs.select',[changedIndex,true,null]); }catch(e){}\n          }\n          try{ if(typeof jq(select).selectpicker==='function') jq(select).selectpicker('refresh'); }catch(e){}\n        }\n      }\n\n      if(toggle.getAttribute('aria-expanded')!=='true'){\n        toggle.click();\n        opened=true;\n        await wait(25);\n      }\n\n      const listId=toggle.getAttribute('aria-owns')||toggle.getAttribute('aria-controls');\n      const list=(listId&&document.getElementById(listId))||wrapper;\n\n      for(const name of names){\n        const options=Array.from(select.options||[]);\n        const option=options.find(function(o){return norm(o.textContent)===norm(name);});\n        if(!option) throw new Error('Tag unavailable: '+name);\n        if(option.disabled) throw new Error('Tag disabled: '+name);\n        const optionIndex=options.indexOf(option);\n\n        if(!option.selected){\n          let item=list.querySelector('li[data-original-index=\"'+optionIndex+'\"] a, li[data-original-index=\"'+optionIndex+'\"] [role=\"option\"]');\n          if(!item){\n            item=Array.from(list.querySelectorAll('a,[role=\"option\"],button,li')).find(function(el){\n              return norm(el.textContent)===norm(name) && el.getAttribute('aria-disabled')!=='true';\n            })||null;\n          }\n          if(!item) throw new Error('Tag menu item not found: '+name);\n\n          // Real option click first.\n          try{ item.click(); }catch(e){}\n          await wait(20);\n\n          // Keep the underlying select definitely in sync with what the UI shows.\n          if(!option.selected) option.selected=true;\n          syncTagSelect(optionIndex);\n          await wait(20);\n        }else{\n          // Even for an already-selected option, sync AgencyZoom's model once.\n          syncTagSelect(optionIndex);\n        }\n      }\n\n      // One final model sync before Save. This is the key v4.21 change: the\n      // tags could look selected in Bootstrap while AgencyZoom's form model was\n      // still unchanged, causing Save to do nothing.\n      syncTagSelect(null);\n\n      if(opened && toggle.getAttribute('aria-expanded')==='true'){\n        toggle.click();\n        opened=false;\n        await wait(20);\n      }\n\n      // Find the EXACT Add Tag container by walking upward from this select.\n      // AgencyZoom's current Add Tag UI is not always a Bootstrap .modal.\n      function findTagDialog(){\n        let el=select;\n        while(el && el!==document.body){\n          if(isVisible(el)){\n            const txt=norm(el.textContent);\n            if(txt.includes('add tag') && txt.includes('choose tags')){\n              const save=Array.from(el.querySelectorAll('button,input[type=\"button\"],input[type=\"submit\"],a,[role=\"button\"]')).find(function(b){\n                return isVisible(b) && !b.disabled && b.getAttribute('aria-disabled')!=='true' && norm(b.value||b.textContent||b.getAttribute('aria-label'))==='save';\n              });\n              if(save) return {root:el,saveBtn:save};\n            }\n          }\n          el=el.parentElement;\n        }\n\n        // Fallback: smallest visible page container that contains Add Tag,\n        // Choose tags and a visible Save button.\n        const saves=Array.from(document.querySelectorAll('button,input[type=\"button\"],input[type=\"submit\"],a,[role=\"button\"]')).filter(function(b){\n          return isVisible(b) && !b.disabled && b.getAttribute('aria-disabled')!=='true' && norm(b.value||b.textContent||b.getAttribute('aria-label'))==='save';\n        });\n        for(const saveBtn of saves){\n          let root=saveBtn.parentElement;\n          for(let i=0;root && root!==document.body && i<8;i++,root=root.parentElement){\n            const txt=norm(root.textContent);\n            if(txt.includes('add tag') && txt.includes('choose tags')) return {root,saveBtn};\n          }\n        }\n        return null;\n      }\n\n      const tagDialog=findTagDialog();\n      if(!tagDialog) throw new Error('Tags selected, but the Add Tag Save button was not found');\n      const dialog=tagDialog.root;\n      let saveBtn=tagDialog.saveBtn;\n\n      // Make sure AgencyZoom receives the final selected values immediately\n      // before the Save handler reads them.\n      syncTagSelect(null);\n      await wait(20);\n\n      function clickSave(btn){\n        if(!btn) return false;\n        try{ btn.focus(); }catch(e){}\n        try{ btn.click(); return true; }catch(e){}\n        if(jq){\n          try{ jq(btn).trigger('click'); return true; }catch(e){}\n        }\n        return false;\n      }\n\n      let clicked=clickSave(saveBtn);\n      if(!clicked) throw new Error('AgencyZoom tag Save button could not be clicked');\n\n      // Fast confirmation: most saves close the Add Tag panel in under 1 second.\n      let closed=await waitFor(function(){ return !isVisible(dialog) ? true : null; },150);\n\n      if(!closed){\n        // If click alone did not invoke the form submission, submit the exact\n        // form containing the Save button. This preserves AgencyZoom validation.\n        const form=saveBtn.closest('form');\n        if(form){\n          try{\n            if(typeof form.requestSubmit==='function') form.requestSubmit(saveBtn);\n            else form.dispatchEvent(new PageEvent('submit',{bubbles:true,cancelable:true}));\n          }catch(e){console.warn('[TritoX TM] Tag form submit fallback:',e);}\n        }else if(jq){\n          try{ jq(saveBtn).trigger('click'); }catch(e){}\n        }\n        closed=await waitFor(function(){ return !isVisible(dialog) ? true : null; },200);\n      }\n\n      if(!closed) throw new Error('Tags are selected, but AgencyZoom did not save/close the Add Tag window');\n\n      // Preserve existing tags.\n      const afterValues=Array.from(select.selectedOptions||[]).map(function(o){return String(o.value);});\n      if(!beforeValues.every(function(v){return afterValues.includes(v);})){ \n        throw new Error('Existing tags changed; review the lead tags');\n      }\n\n      return {ok:true,message:'Tags saved automatically: '+names.join(' + ')+(data.star_rating && !starTagName?' (Star tag option not found)':'')};\n    }catch(error){\n      console.error('[TritoX TM] Quote tags:',error);\n      return {ok:false,message:String(error.message||error)};\n    }finally{\n      if(opened && toggle && toggle.getAttribute('aria-expanded')==='true') toggle.click();\n    }\n  }\n\n  function findControlByLabel(labelText){\n    const wanted=String(labelText||'').toLowerCase().replace(/[^a-z0-9]+/g,' ').trim();\n    const labels=Array.from(document.querySelectorAll('label,.control-label,[class*=\"label\"]'));\n    for(const lab of labels){\n      const txt=String(lab.textContent||'').toLowerCase().replace(/[^a-z0-9]+/g,' ').trim();\n      if(txt!==wanted && !txt.startsWith(wanted)) continue;\n      if(lab.htmlFor){\n        const byFor=document.getElementById(lab.htmlFor);\n        if(byFor && /^(INPUT|SELECT|TEXTAREA)$/.test(byFor.tagName)) return byFor;\n      }\n      const containers=[lab.parentElement,lab.closest('.form-group'),lab.closest('.az-form-group'),lab.closest('[class*=\"form-group\"]')].filter(Boolean);\n      for(const c of containers){\n        const el=c.querySelector('input,select,textarea');\n        if(el) return el;\n      }\n    }\n    return null;\n  }\n\n  function setAnyControl(el,value){\n    if(!el || value===undefined || value===null || String(value)==='') return false;\n    const wanted=String(value).trim();\n    try{\n      if(el.tagName==='SELECT'){\n        const options=Array.from(el.options||[]);\n        let opt=options.find(function(o){return String(o.value).trim().toLowerCase()===wanted.toLowerCase();});\n        if(!opt) opt=options.find(function(o){return String(o.textContent||'').trim().toLowerCase()===wanted.toLowerCase();});\n        if(!opt && /^[123]$/.test(wanted)) opt=options.find(function(o){return new RegExp('^\\\\s*'+wanted+'(?:\\\\s|$)').test(String(o.textContent||''));});\n        if(!opt && /^bw$/i.test(wanted)) opt=options.find(function(o){return /\\\\bBW\\\\b|Bristol West/i.test(String(o.textContent||''));});\n        if(!opt) return false;\n        el.value=opt.value;\n        opt.selected=true;\n        el.dispatchEvent(new Event('input',{bubbles:true}));\n        el.dispatchEvent(new Event('change',{bubbles:true}));\n        return true;\n      }\n      const proto=el.tagName==='TEXTAREA'?window.HTMLTextAreaElement.prototype:window.HTMLInputElement.prototype;\n      const desc=Object.getOwnPropertyDescriptor(proto,'value');\n      if(desc&&desc.set) desc.set.call(el,wanted); else el.value=wanted;\n      el.dispatchEvent(new Event('input',{bubbles:true}));\n      el.dispatchEvent(new Event('change',{bubbles:true}));\n      el.dispatchEvent(new Event('blur',{bubbles:true}));\n      return true;\n    }catch(e){console.warn('[TritoX TM] Could not fill',labelText,value,e);return false;}\n  }\n\n  function findStarControl(){\n    // First use the visible field label.\n    let el=findControlByLabel('Star 1-3') || findControlByLabel('Star 1 - 3') || findControlByLabel('Star');\n    if(el && el.tagName==='SELECT') return el;\n\n    // AgencyZoom sometimes renders the visible Bootstrap control separately\n    // from its real <select>. Find a select in the same field container.\n    const labels=Array.from(document.querySelectorAll('label,.control-label,[class*=\"label\"]'));\n    for(const lab of labels){\n      const txt=String(lab.textContent||'').toLowerCase().replace(/[^a-z0-9]+/g,' ').trim();\n      if(!txt.includes('star')) continue;\n      let c=lab.closest('.form-group,[class*=\"form-group\"],.row,.col-md-6,.col-sm-6') || lab.parentElement;\n      if(!c) continue;\n      const sel=c.querySelector('select');\n      if(sel) return sel;\n    }\n\n    // Final fallback: identify the unique select whose options look like\n    // 1/2/3 stars or BW. This avoids relying on a brittle AgencyZoom field id.\n    const candidates=Array.from(document.querySelectorAll('select')).filter(function(sel){\n      const text=Array.from(sel.options||[]).map(function(o){return String(o.textContent||'').trim();}).join(' | ');\n      const has1=/(^|\\|)\\s*1(?:\\s*star)?\\s*(\\||$)/i.test(text);\n      const has2=/(^|\\|)\\s*2(?:\\s*stars?)?\\s*(\\||$)/i.test(text);\n      const has3=/(^|\\|)\\s*3(?:\\s*stars?)?\\s*(\\||$)/i.test(text);\n      const hasBW=/\\bBW\\b|Bristol West/i.test(text);\n      return has1 && has2 && has3 && hasBW;\n    });\n    return candidates.length===1 ? candidates[0] : (candidates[0]||el||null);\n  }\n\n  function setStarControl(value){\n    if(value===undefined || value===null || String(value).trim()==='') return false;\n    const wanted=String(value).trim();\n    const el=findStarControl();\n    if(!el) return false;\n    if(el.tagName!=='SELECT') return setAnyControl(el,wanted);\n\n    try{\n      const options=Array.from(el.options||[]);\n      let opt=options.find(function(o){return String(o.value||'').trim().toLowerCase()===wanted.toLowerCase();});\n      if(!opt && /^[123]$/.test(wanted)){\n        opt=options.find(function(o){\n          const t=String(o.textContent||'').trim();\n          return new RegExp('^'+wanted+'(?:\\s*stars?)?$', 'i').test(t) || new RegExp('^'+wanted+'(?:\\s|$)').test(t);\n        });\n      }\n      if(!opt && /^bw$/i.test(wanted)) opt=options.find(function(o){return /\\bBW\\b|Bristol West/i.test(String(o.textContent||''));});\n      if(!opt) return false;\n\n      // Preserve normal AgencyZoom behavior and refresh Bootstrap-select UI.\n      Array.from(el.options||[]).forEach(function(o){o.selected=(o===opt);});\n      el.value=opt.value;\n      el.dispatchEvent(new Event('input',{bubbles:true}));\n      el.dispatchEvent(new Event('change',{bubbles:true}));\n      const pageWindow=typeof unsafeWindow!=='undefined'?unsafeWindow:window;\n      const jq=pageWindow.jQuery;\n      if(jq){\n        try{\n          const $el=jq(el);\n          $el.val(opt.value).trigger('change');\n          if(typeof $el.selectpicker==='function') $el.selectpicker('refresh');\n        }catch(e){}\n      }\n      return String(el.value)===String(opt.value) || !!opt.selected;\n    }catch(e){\n      console.warn('[TritoX TM] Star field fill failed:',e);\n      return false;\n    }\n  }\n\n  function fillAltaMetadataFields(d){\n    const results={company:false,date:false,star:false};\n    if(d.current_company){\n      results.company=setAnyControl(findControlByLabel('Current Company'),d.current_company);\n    }\n    if(d.auto_renewal_date){\n      results.date=setAnyControl(findControlByLabel('Auto renewal date'),d.auto_renewal_date);\n    }\n    if(d.star_rating){\n      results.star=setStarControl(d.star_rating);\n      // A short second pass handles AgencyZoom fields that finish rendering\n      // just after the rest of Main is available.\n      if(!results.star){\n        setTimeout(function(){\n          const ok=setStarControl(d.star_rating);\n          console.log('[TritoX TM] Star retry:',ok,d.star_rating);\n        },250);\n      }\n    }\n    console.log('[TritoX TM] ALTA metadata autofill:',results,d.current_company,d.auto_renewal_date,d.star_rating);\n    return results;\n  }\n\n  function fillFields(d){\n    fillText('cf30203', d.vehicles_policy);\n    fillSelect('cf56698', d.bodily_injury);\n    fillText('cf47028', d.home_coverage_a);\n    fillText('cf37981', d.home_annual);\n    fillText('cf56654', d.auto1);\n    fillSelect('cf56655', d.auto1_ded);\n    fillText('cf56656', d.auto2);\n    fillSelect('cf56657', d.auto2_ded);\n    fillText('cf56692', d.auto3);\n    fillSelect('cf56693', d.auto3_ded);\n    fillText('cf56694', d.auto4);\n    fillSelect('cf56695', d.auto4_ded);\n    fillText('cf56696', d.auto5);\n    fillSelect('cf56697', d.auto5_ded);\n    fillAltaMetadataFields(d);\n    function fillMoneyFields(attempt){\n      const mEl = document.querySelector('input[name=\"customFields[cf30197]\"]');\n      const sEl = document.querySelector('input[name=\"customFields[cf30199]\"]');\n      if(mEl){\n        const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set;\n        setter.call(mEl, String(d.monthly_auto).replace(/[$,]/g,''));\n        mEl.dispatchEvent(new Event('input',{bubbles:true}));\n        mEl.dispatchEvent(new Event('change',{bubbles:true}));\n      }\n      if(sEl){\n        const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set;\n        setter.call(sEl, String(d.auto_6months).replace(/[$,]/g,''));\n        sEl.dispatchEvent(new Event('input',{bubbles:true}));\n        sEl.dispatchEvent(new Event('change',{bubbles:true}));\n      }\n      if((!mEl || !sEl) && attempt < 30){\n        setTimeout(function(){ fillMoneyFields(attempt+1); }, 800);\n      }\n    }\n    fillMoneyFields(1);\n  }\n\n  let filledTs = 0; // timestamp of last fill action\n  let lastSeenDataTs = 0;\n\n  function refreshFillButton(){\n    const gmRaw = GM_getValue('tritox_az_data','');\n    if(!gmRaw) return;\n    let gmData;\n    try{ gmData = JSON.parse(gmRaw); }catch(e){ return; }\n    const gmTs = Number(gmData && gmData._ts || 0);\n    if(!gmTs || gmTs <= filledTs || gmTs <= expiredTs) return;\n\n    // New data must replace any old Fill button even when the AgencyZoom URL\n    // and lead panel do not rerender.\n    if(gmTs !== lastSeenDataTs){\n      lastSeenDataTs = gmTs;\n      const existing=document.getElementById('tritox-fill-btn');\n      if(existing) existing.remove();\n      const oldOverlay=document.getElementById('tritox-fill-overlay');\n      if(oldOverlay) oldOverlay.remove();\n    }\n    addFillButton();\n  }\n\n  // Fast poll so the popup appears almost immediately after QC finishes.\n  setInterval(refreshFillButton, 250);\n  setTimeout(refreshFillButton, 100);\n\n  // AgencyZoom is a SPA; rerenders can remove fixed DOM nodes. Re-add the\n  // current Fill button after DOM changes without waiting for a navigation.\n  const tmObserver=new MutationObserver(function(){\n    if(!document.getElementById('tritox-fill-btn')) refreshFillButton();\n  });\n  tmObserver.observe(document.documentElement,{childList:true,subtree:true});\n\n})();\n";

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
    +'1. Install Tampermonkey<br>'
    +'2. Replace your existing TritoX script with this version<br>'
    +'3. Refresh ALTA, Aaron QC and AgencyZoom<br>'
    +'4. In ALTA, confirm Current Company / Renewal Date / Star-BW. The lead name and ALTA ID are detected automatically.<br>'
    +'5. Process the PDF in Aaron QC, then use Fill + Attach in AgencyZoom.'
    +'</div>'
    +'<button id="tmCopyBtn" style="background:linear-gradient(135deg,#00d4ff,#7b2fff);color:#fff;border:none;border-radius:8px;padding:8px 18px;font-size:12px;font-weight:700;cursor:pointer;margin-bottom:12px;">📋 Copy Script</button>'
    +'<pre id="tmScriptPre" style="background:#080a0f;border:1px solid #252d45;border-radius:8px;padding:14px;font-size:11px;color:#dce4f5;white-space:pre-wrap;overflow-x:auto;max-height:350px;overflow-y:auto;"></pre>';
  modal.appendChild(box);
  document.body.appendChild(modal);
  document.getElementById('tmScriptPre').textContent = script;
  document.getElementById('tmCopyBtn').addEventListener('click', function(){
    navigator.clipboard.writeText(script).then(function(){
      document.getElementById('tmCopyBtn').textContent = '✅ Copied!';
      setTimeout(function(){ document.getElementById('tmCopyBtn').textContent = '📋 Copy Script'; }, 2000);
    });
  });
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
