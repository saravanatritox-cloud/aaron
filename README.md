<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>TritoX Tampermonkey v4.46.19 - PIP Driver Under 65</title>
<style>
  body{font-family:Arial,sans-serif;margin:0;background:#f6f7fb;color:#172033}
  .wrap{max-width:1200px;margin:28px auto;padding:0 18px}
  .card{background:#fff;border:1px solid #dfe3eb;border-radius:14px;box-shadow:0 4px 18px rgba(0,0,0,.06);overflow:hidden}
  .head{padding:20px 22px;border-bottom:1px solid #e8ebf1}
  h1{margin:0 0 6px;font-size:22px}
  p{margin:0;color:#667085;line-height:1.5}
  .actions{padding:14px 22px;border-bottom:1px solid #e8ebf1;display:flex;gap:10px;align-items:center}
  button{border:0;border-radius:8px;padding:10px 16px;font-weight:700;cursor:pointer;background:#6d3cff;color:#fff}
  pre{margin:0;padding:22px;overflow:auto;white-space:pre;tab-size:2;font-family:Consolas,Monaco,monospace;font-size:13px;line-height:1.5}
  .note{font-size:12px;color:#667085}
</style>
</head>
<body>
<div class="wrap">
  <div class="card">
    <div class="head">
      <h1>TritoX Tampermonkey v4.46.19</h1>
      <p>PIP Driver Under 65 helper — accepted drivers only, main driver first, otherwise one accepted driver under age 65.</p>
    </div>
    <div class="actions">
      <button onclick="navigator.clipboard.writeText(document.getElementById('code').innerText).then(()=>this.textContent='Copied ✓')">Copy Tampermonkey Script</button>
      <span class="note">Copy the script below into Tampermonkey.</span>
    </div>
    <pre id="code">// ==UserScript==
// @name         TritoX AgencyZoom Auto-Fill
// @namespace    http://tampermonkey.net/
// @version      4.46.19-pip-driver-under65
// @description  Stable rollback: original QC field fill + PDF + working automatic tag save
// @match        https://app.agencyzoom.com/*
// @match        https://alta.farmers.com/*
// @match        https://tritoxtech.github.io/*
// @match        https://saravanatritox-cloud.github.io/aaron/*
// @grant        GM_setValue
// @grant        GM_getValue
// @grant        GM_deleteValue
// @grant        GM_addStyle
// @grant        unsafeWindow
// ==/UserScript==

(function(){
  &#x27;use strict&#x27;;

  console.log(&#x27;[TritoX TM] v4.46.19 PIP driver under-65 hostname:&#x27;, window.location.hostname);


  // ────────────────────────────────────────────────────────────────────────────
  // ALTA — capture extra lead metadata once and keep it while navigating pages.
  // Customer name + ALTA ID are read automatically; the user only confirms the
  // carrier, renewal date and Star/BW value.
  // ────────────────────────────────────────────────────────────────────────────
  if(window.location.hostname === &#x27;alta.farmers.com&#x27;){
    const CARRIERS=[
      &#x27;AAA&#x27;,&#x27;Allstate&#x27;,&#x27;Auto-Owners Insurance&#x27;,&#x27;Bristol West&#x27;,&#x27;Farm Bureau&#x27;,&#x27;GEICO&#x27;,
      &#x27;Liberty Mutual&#x27;,&#x27;Nationwide&#x27;,&#x27;Progressive&#x27;,&#x27;State Farm&#x27;,&#x27;Travelers&#x27;,&#x27;USAA&#x27;
    ];

    function cleanText(v){ return String(v||&#x27;&#x27;).replace(/\s+/g,&#x27; &#x27;).trim(); }
    function normName(v){ return cleanText(v).toLowerCase().replace(/[^a-z0-9]+/g,&#x27; &#x27;).trim(); }

    function altaIdentity(){
      const text=document.body ? document.body.innerText : &#x27;&#x27;;
      const idm=text.match(/Alta\s*#\s*(\d{8,})/i);
      const id=idm?idm[1]:&#x27;&#x27;;
      let name=&#x27;&#x27;;
      const nm=text.match(/(?:^|\n)\s*([^\n]{2,80}?)\s*-\s*Auto\s*(?:\n|$)/i);
      if(nm) name=cleanText(nm[1]);
      if(!name){
        const nm2=text.match(/([A-Za-z][A-Za-z .&#x27;-]{2,70})\s*-\s*Auto\s+Alta\s*#/i);
        if(nm2) name=cleanText(nm2[1]);
      }
      return {name,id};
    }

    const ALTA_META_TTL=7200000; // same 2-hour validity window as Aaron autofill popup

    function priorInsuranceSnapshot(){
      const text=document.body ? document.body.innerText : &#x27;&#x27;;
      const start=text.search(/Prior insurance information/i);
      if(start&lt;0) return {visible:false,inEffect:false,company:&#x27;&#x27;,renewalDate:&#x27;&#x27;};
      const chunk=text.slice(start,start+3000);

      // ALTA can show several prior policies. Always use the TOP/FIRST policy
      // that is explicitly marked &quot;In Effect&quot;. Do not choose a carrier merely
      // because its name appears somewhere later in the prior-insurance list.
      const lines=chunk.split(/\n+/).map(function(v){return cleanText(v);}).filter(Boolean);
      let company=&#x27;&#x27;;
      let renewalDate=&#x27;&#x27;;
      let foundInEffect=false;

      for(let i=0;i&lt;lines.length;i++){
        if(!/\bIn Effect\b/i.test(lines[i])) continue;
        foundInEffect=true;

        // Build a small row window around this FIRST In Effect marker. In ALTA,
        // the carrier/status/date may be on one line or split across nearby lines.
        const from=Math.max(0,i-2);
        const to=Math.min(lines.length,i+4);
        const row=lines.slice(from,to).join(&#x27; &#x27;);

        // Prefer the carrier whose text is physically closest to this row.
        // This preserves ALTA&#x27;s on-screen order (top row wins).
        let best=null;
        for(const carrier of CARRIERS){
          const rx=new RegExp(&#x27;\\b&#x27;+carrier.replace(/[.*+?^${}()|[\]\\]/g,&#x27;\\$&amp;&#x27;)+&#x27;\\b&#x27;,&#x27;i&#x27;);
          const m=rx.exec(row);
          if(m &amp;&amp; (!best || m.index&lt;best.index)) best={name:carrier,index:m.index};
        }
        if(best) company=best.name;

        if(!company){
          // Generic fallback: take text immediately before &quot;In Effect&quot; from
          // the same line, then clean off table labels/numbers if present.
          const same=lines[i].match(/^(.{2,80}?)\s+In Effect\b/i);
          if(same){
            let candidate=cleanText(same[1]);
            candidate=candidate.replace(/^(?:Prior insurance information|Driver|Drivers|Vehicles|Tenure|Coverage Term|BI|PD)\s*/i,&#x27;&#x27;).trim();
            candidate=candidate.replace(/^\d+\s+/,&#x27;&#x27;).trim();
            if(candidate) company=candidate;
          }
        }

        // Renewal date must come from the SAME first In Effect policy.
        // ALTA often renders the carrier/status on one DOM line and the
        // Coverage Term several lines later, so the old 4-line window could
        // miss the date even though the top policy was detected correctly.
        let dm=row.match(/\b(\d{1,2}\/\d{1,2}\/\d{4})\s*-\s*(\d{1,2}\/\d{1,2}\/\d{4})\b/);
        if(!dm){
          const firstEffectPos=chunk.search(/\bIn Effect\b/i);
          if(firstEffectPos&gt;=0){
            const afterFirst=chunk.slice(firstEffectPos);
            const nextRel=afterFirst.slice(1).search(/\bIn Effect\b/i);
            const firstPolicyText=nextRel&gt;=0
              ? afterFirst.slice(0,nextRel+1)
              : afterFirst.slice(0,700);
            dm=firstPolicyText.match(/\b(\d{1,2}\/\d{1,2}\/\d{4})\s*-\s*(\d{1,2}\/\d{1,2}\/\d{4})\b/);
          }
        }
        if(dm) renewalDate=dm[2];
        break; // critical: never fall through to Progressive/another lower row
      }

      if(!foundInEffect) return {visible:true,inEffect:false,company:&#x27;&#x27;,renewalDate:&#x27;&#x27;};
      return {visible:true,inEffect:true,company:company,renewalDate:renewalDate};
    }

    function parseFullDob(value){
      const m=cleanText(value).match(/^(\d{1,2})\/(\d{1,2})\/(\d{4})$/);
      if(!m) return null;
      const month=Number(m[1]), day=Number(m[2]), year=Number(m[3]);
      if(month&lt;1||month&gt;12||day&lt;1||day&gt;31||year&lt;1900||year&gt;new Date().getFullYear()) return null;
      const d=new Date(year,month-1,day);
      if(d.getFullYear()!==year||d.getMonth()!==month-1||d.getDate()!==day) return null;
      return d;
    }

    function ageFromDob(dob){
      if(!(dob instanceof Date) || isNaN(dob)) return null;
      const now=new Date();
      let age=now.getFullYear()-dob.getFullYear();
      const beforeBirthday=(now.getMonth()&lt;dob.getMonth()) ||
        (now.getMonth()===dob.getMonth() &amp;&amp; now.getDate()&lt;dob.getDate());
      if(beforeBirthday) age--;
      return age;
    }

    function driverCardForDobInput(dobInput){
      let el=dobInput;
      for(let depth=0; el &amp;&amp; depth&lt;9; depth++,el=el.parentElement){
        const controls=el.querySelectorAll ? el.querySelectorAll(&#x27;input,select,[role=&quot;combobox&quot;]&#x27;) : [];
        const text=cleanText(el.innerText||&#x27;&#x27;);
        if(controls.length&gt;=5 &amp;&amp; /Accepted|Driver status|Relationship to PNI|On current policy/i.test(text)) return el;
      }
      return dobInput.parentElement;
    }

    function selectedControlText(el){
      if(!el) return &#x27;&#x27;;
      if(el.tagName===&#x27;SELECT&#x27;){
        const opt=el.options &amp;&amp; el.selectedIndex&gt;=0 ? el.options[el.selectedIndex] : null;
        return cleanText(opt ? opt.textContent : el.value);
      }
      return cleanText(el.value || el.getAttribute(&#x27;aria-label&#x27;) || el.textContent || &#x27;&#x27;);
    }

    function driverSnapshot(){
      const bodyText=document.body ? document.body.innerText : &#x27;&#x27;;
      if(!/Rated drivers\s*\(/i.test(bodyText)) return {visible:false,drivers:[],selected:null};

      const dobInputs=Array.from(document.querySelectorAll(&#x27;input&#x27;)).filter(function(el){
        const v=cleanText(el.value);
        return /^\d{1,2}\/\d{1,2}\/(?:\d{4}|\*{4})$/.test(v);
      });

      const seen=new Set();
      const drivers=[];

      dobInputs.forEach(function(dobInput,index){
        const card=driverCardForDobInput(dobInput);
        if(!card || seen.has(card)) return;
        seen.add(card);

        const dobText=cleanText(dobInput.value);
        const dob=parseFullDob(dobText);
        const age=ageFromDob(dob);
        const cardInputs=Array.from(card.querySelectorAll(&#x27;input&#x27;)).filter(function(x){return x!==dobInput;});
        const nameValues=cardInputs.map(function(x){return cleanText(x.value);}).filter(function(v){
          return /^[A-Za-z][A-Za-z .&#x27;-]{0,60}$/.test(v) &amp;&amp; !/^(United States|MI|Accepted|Self|Other)$/i.test(v);
        });
        const first=nameValues[0]||&#x27;&#x27;;
        const last=nameValues[1]||&#x27;&#x27;;
        const name=cleanText((first+&#x27; &#x27;+last).trim());

        const choices=Array.from(card.querySelectorAll(&#x27;select,[role=&quot;combobox&quot;]&#x27;)).map(selectedControlText).filter(Boolean);
        const cardText=cleanText(card.innerText||&#x27;&#x27;);
        const accepted=choices.some(function(v){return /^Accepted$/i.test(v);}) || /\bAccepted\b/i.test(cardText);
        const self=choices.some(function(v){return /^Self$/i.test(v);}) || /\bSelf\b/i.test(cardText);

        drivers.push({index:index,name:name,dob:dobText,age:age,accepted:accepted,self:self,fullDob:!!dob});
      });

      const accepted=drivers.filter(function(d){return d.accepted;});
      const main=accepted.find(function(d){return d.self;}) || accepted[0] || null;
      let selected=null;
      if(main &amp;&amp; main.fullDob &amp;&amp; main.age&lt;65){
        selected=main;
      }else if(main &amp;&amp; main.fullDob &amp;&amp; main.age&gt;=65){
        selected=accepted.find(function(d){return d!==main &amp;&amp; d.fullDob &amp;&amp; d.age&lt;65;}) || null;
      }else{
        selected=accepted.find(function(d){return d!==main &amp;&amp; d.fullDob &amp;&amp; d.age&lt;65;}) || null;
      }
      return {visible:true,drivers:drivers,main:main,selected:selected};
    }

    function detectCarrier(){ return priorInsuranceSnapshot().company; }
    function detectRenewalDate(){ return priorInsuranceSnapshot().renewalDate; }

    function isBwCoveragePage(){
      // ALTA&#x27;s Bristol West coverage route is explicit. Check pathname first so
      // page text, stale SPA content, or quote labels can never override BW.
      return /\/quote\/auto\/coverages-review-bw(?:\/)?$/i.test(location.pathname) ||
        /\/quote\/auto\/coverages-review-bw(?:[/?#]|$)/i.test(location.href);
    }

    function onAutoCoverageSection(){
      return isBwCoveragePage() || /\/quote\/auto\/coverages-review(?:\/)?$/i.test(location.pathname) ||
        /\/quote\/auto\/coverages-review(?:[/?#]|$)/i.test(location.href);
    }

    // ── ALTA coverage presets ────────────────────────────────────────────────
    // Apply only after the Auto coverages page has rendered. Farmers and
    // Bristol West use separate presets. The selected values remain visible in
    // ALTA because the real page controls are changed and normal change events
    // are dispatched.
    const FARMERS_COVERAGE_PRESET=[
      [&#x27;Bodily injury&#x27;,&#x27;$100,000/$300,000&#x27;],
      [&#x27;Property damage&#x27;,&#x27;$100,000&#x27;],
      [&#x27;UM/UIM - bodily injury&#x27;,&#x27;$100,000/$300,000&#x27;]
    ];
    const BW_COVERAGE_PRESET=[
      [&#x27;Bodily injury&#x27;,&#x27;$100,000/$300,000&#x27;],
      [&#x27;Property damage&#x27;,&#x27;$100,000&#x27;],
      [&#x27;Limited property damage&#x27;,&#x27;$3,000&#x27;],
      [&#x27;Uninsured motorist - bodily injury&#x27;,&#x27;$100,000/$300,000&#x27;],
      [&#x27;Underinsured motorist - bodily injury&#x27;,&#x27;$100,000/$300,000&#x27;]
    ];

    function covNorm(v){
      return cleanText(v).toLowerCase().replace(/\$/g,&#x27;&#x27;).replace(/,/g,&#x27;&#x27;).replace(/\s+/g,&#x27;&#x27;).replace(/[–—]/g,&#x27;-&#x27;);
    }
    function covLabelNorm(v){
      return cleanText(v).toLowerCase().replace(/[^a-z0-9/]+/g,&#x27; &#x27;).trim();
    }
    function visibleEl(el){
      if(!el) return false;
      try{
        const r=el.getBoundingClientRect();
        const s=getComputedStyle(el);
        return r.width&gt;0 &amp;&amp; r.height&gt;0 &amp;&amp; s.display!==&#x27;none&#x27; &amp;&amp; s.visibility!==&#x27;hidden&#x27;;
      }catch(e){ return false; }
    }

    function coverageSelectForLabel(label){
      const wanted=covLabelNorm(label);
      let best=null;
      for(const sel of Array.from(document.querySelectorAll(&#x27;select&#x27;))){
        if(!visibleEl(sel) &amp;&amp; !visibleEl(sel.parentElement)) continue;
        let node=sel.parentElement;
        for(let depth=0;node &amp;&amp; depth&lt;6;depth++,node=node.parentElement){
          const txt=covLabelNorm(node.innerText||&#x27;&#x27;);
          if(txt.includes(wanted)){
            const noise=Math.max(0,txt.length-wanted.length);
            const score=depth*100+noise;
            if(!best || score&lt;best.score) best={el:sel,score:score};
            break;
          }
        }
      }
      return best?best.el:null;
    }

    function optionForValue(select,wanted){
      const wn=covNorm(wanted);
      const opts=Array.from(select.options||[]);
      return opts.find(function(o){return covNorm(o.textContent||o.label||o.value)===wn;}) ||
        opts.find(function(o){return covNorm(o.value)===wn;}) || null;
    }

    function setCoverageNative(select,wanted){
      if(!select) return false;
      const opt=optionForValue(select,wanted);
      if(!opt) return false;
      if(String(select.value)===String(opt.value) &amp;&amp; opt.selected) return true;
      try{
        const setter=Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,&#x27;value&#x27;);
        if(setter&amp;&amp;setter.set) setter.set.call(select,opt.value); else select.value=opt.value;
        Array.from(select.options||[]).forEach(function(o){o.selected=(o===opt);});
        select.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
        select.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
        select.dispatchEvent(new Event(&#x27;blur&#x27;,{bubbles:true}));
        return true;
      }catch(e){
        console.warn(&#x27;[TritoX TM] ALTA coverage native select failed:&#x27;,wanted,e);
        return false;
      }
    }

    function coverageContainerForLabel(label){
      const wanted=covLabelNorm(label);
      const els=Array.from(document.querySelectorAll(&#x27;label,div,span,p,td&#x27;));
      let best=null;
      for(const el of els){
        if(!visibleEl(el)) continue;
        const own=covLabelNorm(el.textContent||&#x27;&#x27;);
        if(own!==wanted) continue;
        let node=el.parentElement;
        for(let depth=0;node &amp;&amp; depth&lt;5;depth++,node=node.parentElement){
          const controls=node.querySelectorAll(&#x27;select,button,[role=&quot;combobox&quot;],input&#x27;);
          if(controls.length){
            const score=depth*100+(node.innerText||&#x27;&#x27;).length;
            if(!best||score&lt;best.score) best={el:node,score:score};
            break;
          }
        }
      }
      return best?best.el:null;
    }

    async function setCoverageFallback(label,wanted){
      const row=coverageContainerForLabel(label);
      if(!row) return false;
      const control=Array.from(row.querySelectorAll(&#x27;button,[role=&quot;combobox&quot;]&#x27;)).find(visibleEl);
      if(!control) return false;
      try{ control.click(); }catch(e){ return false; }
      await new Promise(function(resolve){setTimeout(resolve,60);});
      const wn=covNorm(wanted);
      const options=Array.from(document.querySelectorAll(&#x27;[role=&quot;option&quot;],mat-option,.mat-option,.dropdown-menu li a,.dropdown-menu li button,li[role=&quot;option&quot;]&#x27;))
        .filter(visibleEl);
      const target=options.find(function(el){return covNorm(el.textContent||&#x27;&#x27;)===wn;});
      if(!target) return false;
      try{ target.click(); return true; }catch(e){ return false; }
    }

    let coveragePresetBusy=false;
    let coveragePresetDoneSig=&#x27;&#x27;;
    async function applyAltaCoverageDefaults(){
      if(!onAutoCoverageSection() || coveragePresetBusy) return;
      const ident=altaIdentity();
      const sig=(ident.id||&#x27;&#x27;)+&#x27;|&#x27;+location.pathname;
      if(sig===coveragePresetDoneSig) return;
      coveragePresetBusy=true;
      try{
        const preset=isBwCoveragePage()?BW_COVERAGE_PRESET:FARMERS_COVERAGE_PRESET;
        let allDone=true;
        for(const pair of preset){
          const label=pair[0],wanted=pair[1];
          const sel=coverageSelectForLabel(label);
          let ok=false;
          if(sel){
            const opt=optionForValue(sel,wanted);
            if(opt &amp;&amp; covNorm(sel.options[sel.selectedIndex]&amp;&amp;sel.options[sel.selectedIndex].textContent)===covNorm(wanted)) ok=true;
            else ok=setCoverageNative(sel,wanted);
          }
          if(!ok) ok=await setCoverageFallback(label,wanted);
          if(!ok) allDone=false;
          await new Promise(function(resolve){setTimeout(resolve,30);});
        }
        if(allDone){
          coveragePresetDoneSig=sig;
          console.log(&#x27;[TritoX TM] ALTA coverage preset applied:&#x27;,isBwCoveragePage()?&#x27;BW&#x27;:&#x27;Farmers&#x27;);
        }
      }finally{
        coveragePresetBusy=false;
      }
    }

    function detectStar(){
      // Rating is detected ONLY on ALTA&#x27;s Auto coverages page.
      if(!onAutoCoverageSection()) return &#x27;&#x27;;

      // IMPORTANT: resolve Bristol West from the route BEFORE scanning text.
      // The BW page can contain numbers such as Opt. 1 / Opt. 3 and other
      // content that must never be interpreted as a Farmers star rating.
      if(isBwCoveragePage()) return &#x27;BW&#x27;;

      const text=document.body ? document.body.innerText : &#x27;&#x27;;

      // Farmers rating banner: 1 Star / 2 Stars / 3 Stars.
      const m=text.match(/\b([123])\s*Stars?\b/i);
      if(m) return m[1];

      // Bristol West coverage layout does not show a Star badge. In the BW
      // layout ALTA shows the Bristol West PIP deductible row without the
      // separate Farmers PIP deductible row that is present on Farmers quotes.
      const hasBWPip=/Bristol\s+West\s+PIP\s+deductible/i.test(text);
      const hasFarmersPip=/Farmers\s+PIP\s+deductible/i.test(text);
      if(hasBWPip &amp;&amp; !hasFarmersPip) return &#x27;BW&#x27;;

      // Extra safety: inspect the visible quote banner/logo for Bristol West
      // branding. This helps if ALTA changes the PIP labels later.
      const brandEls=Array.from(document.querySelectorAll(&#x27;img,[aria-label],[title],[data-testid],[class],[id]&#x27;)).filter(function(el){
        try{
          const r=el.getBoundingClientRect();
          return r.width&gt;0 &amp;&amp; r.height&gt;0 &amp;&amp; r.top&lt;260 &amp;&amp; r.bottom&gt;0;
        }catch(e){ return false; }
      });
      const bwBrand=brandEls.some(function(el){
        const bits=[
          el.getAttribute&amp;&amp;el.getAttribute(&#x27;alt&#x27;),
          el.getAttribute&amp;&amp;el.getAttribute(&#x27;title&#x27;),
          el.getAttribute&amp;&amp;el.getAttribute(&#x27;aria-label&#x27;),
          el.getAttribute&amp;&amp;el.getAttribute(&#x27;data-testid&#x27;),
          el.id, el.className,
          el.getAttribute&amp;&amp;el.getAttribute(&#x27;src&#x27;),
          el.style&amp;&amp;el.style.backgroundImage
        ].map(function(v){return String(v||&#x27;&#x27;);}).join(&#x27; &#x27;);
        return /bristol\s*west|bristolwest|(?:^|[^a-z])bw(?:[^a-z]|$)/i.test(bits);
      });
      if(bwBrand) return &#x27;BW&#x27;;

      return &#x27;&#x27;;
    }

    function storageKey(id){ return &#x27;tritox_alta_lead_&#x27;+String(id||&#x27;unknown&#x27;); }
    const ALTA_INDEX_KEY=&#x27;tritox_alta_index&#x27;;

    function readAltaIndex(){
      try{
        const raw=GM_getValue(ALTA_INDEX_KEY,&#x27;{}&#x27;);
        const index=JSON.parse(raw||&#x27;{}&#x27;)||{};
        let changed=false;
        Object.keys(index).forEach(function(id){
          const m=index[id]||{};
          if(!m._savedAt || Date.now()-Number(m._savedAt)&gt;ALTA_META_TTL){
            delete index[id];
            changed=true;
          }
        });
        if(changed) GM_setValue(ALTA_INDEX_KEY,JSON.stringify(index));
        return index;
      }catch(e){ return {}; }
    }

    function readSaved(id){
      try{
        const index=readAltaIndex();
        if(index[id]) return index[id];
        const obj=JSON.parse(GM_getValue(storageKey(id),&#x27;{}&#x27;))||{};
        if(obj._savedAt &amp;&amp; Date.now()-obj._savedAt&gt;ALTA_META_TTL) return {};
        return obj;
      }catch(e){return {};}
    }

    function saveMeta(meta){
      if(!meta || !meta.altaId) return;
      meta._savedAt=Date.now();

      // Keep every lead separately so 4-6 quotes can be worked in parallel.
      // Nothing is overwritten just because another ALTA lead becomes current.
      const index=readAltaIndex();
      index[String(meta.altaId)]=meta;
      GM_setValue(ALTA_INDEX_KEY,JSON.stringify(index));

      // Per-ID key retained for backwards compatibility/debugging.
      GM_setValue(storageKey(meta.altaId),JSON.stringify(meta));
      GM_setValue(&#x27;tritox_alta_latest&#x27;,JSON.stringify(meta));
      console.log(&#x27;[TritoX TM] ALTA metadata auto-saved:&#x27;,meta,&#x27;cached leads:&#x27;,Object.keys(index).length);
    }

    // ── Dummy In-force Insurance ─────────────────────────────────────────────
    // User-triggered only. It never runs automatically.
    // Defaults:
    //   Company: AAA
    //   BI: $100,000/$300,000
    //   Expiration: 6 months from today&#x27;s browser date
    //   Insured with company: 6 - 11 Months
    //   More than 6 months continuous insurance: Yes (auto-selected by ALTA from tenure)
    function txVisible(el){
      if(!el) return false;
      try{
        const r=el.getBoundingClientRect();
        const s=getComputedStyle(el);
        return r.width&gt;0 &amp;&amp; r.height&gt;0 &amp;&amp; s.display!==&#x27;none&#x27; &amp;&amp; s.visibility!==&#x27;hidden&#x27;;
      }catch(e){ return false; }
    }

    function txNorm(v){
      return cleanText(v)
        .toLowerCase()
        .replace(/[–—]/g,&#x27;-&#x27;)
        .replace(/\s*\/\s*/g,&#x27;/&#x27;)
        .replace(/\s*-\s*/g,&#x27;-&#x27;)
        .replace(/\s+/g,&#x27; &#x27;)
        .trim();
    }

    function txPageWindow(){
      try{ return typeof unsafeWindow!==&#x27;undefined&#x27; ? unsafeWindow : window; }
      catch(e){ return window; }
    }

    function txNativeSetInput(input,value){
      if(!input) return false;
      try{
        const pw=txPageWindow();
        const proto=input.tagName===&#x27;TEXTAREA&#x27;
          ? pw.HTMLTextAreaElement.prototype
          : pw.HTMLInputElement.prototype;
        const desc=Object.getOwnPropertyDescriptor(proto,&#x27;value&#x27;);
        if(desc&amp;&amp;desc.set) desc.set.call(input,String(value)); else input.value=String(value);

        const E=pw.Event||Event;
        const IE=pw.InputEvent||InputEvent;
        try{ input.dispatchEvent(new IE(&#x27;input&#x27;,{bubbles:true,cancelable:true,data:String(value),inputType:&#x27;insertText&#x27;})); }
        catch(e){ input.dispatchEvent(new E(&#x27;input&#x27;,{bubbles:true})); }
        input.dispatchEvent(new E(&#x27;change&#x27;,{bubbles:true}));
        return true;
      }catch(e){
        try{ input.value=String(value); input.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true})); input.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true})); return true; }
        catch(_e){ return false; }
      }
    }

    function txFindButton(rx,root){
      root=root||document;
      return Array.from(root.querySelectorAll(&#x27;button,a,[role=&quot;button&quot;],input[type=&quot;button&quot;],input[type=&quot;submit&quot;]&#x27;))
        .filter(txVisible)
        .find(function(el){
          const t=cleanText(el.textContent||el.value||el.getAttribute(&#x27;aria-label&#x27;)||&#x27;&#x27;);
          return rx.test(t);
        })||null;
    }

    function txFindDrawer(){
      const candidates=Array.from(document.querySelectorAll(&#x27;aside,[role=&quot;dialog&quot;],.drawer,.modal,[class*=&quot;drawer&quot;],[class*=&quot;panel&quot;],div&#x27;))
        .filter(txVisible)
        .filter(function(el){
          const t=cleanText(el.innerText||&#x27;&#x27;);
          return /^In-force policy\b/i.test(t) || (/\bIn-force policy\b/i.test(t) &amp;&amp; /Current insurance company/i.test(t));
        });
      if(!candidates.length) return null;
      // Prefer the smallest visible container containing the complete form.
      candidates.sort(function(a,b){
        return (a.getBoundingClientRect().width*a.getBoundingClientRect().height)-
               (b.getBoundingClientRect().width*b.getBoundingClientRect().height);
      });
      return candidates[0];
    }

    function txWaitFor(getter,timeout){
      return new Promise(function(resolve){
        let finished=false;
        let observer=null;
        let fallbackTimer=null;
        let timeoutTimer=null;

        function finish(value){
          if(finished) return;
          finished=true;
          try{ if(observer) observer.disconnect(); }catch(e){}
          try{ if(fallbackTimer) clearInterval(fallbackTimer); }catch(e){}
          try{ if(timeoutTimer) clearTimeout(timeoutTimer); }catch(e){}
          resolve(value||null);
        }

        function check(){
          if(finished) return;
          try{
            const v=getter();
            if(v){ finish(v); return; }
          }catch(e){}
        }

        check();
        if(finished) return;

        // MutationObserver continues reacting to DOM changes when the ALTA tab
        // is in the background, unlike very short setTimeout polling loops which
        // Chrome heavily throttles.
        try{
          observer=new MutationObserver(check);
          observer.observe(document.documentElement,{
            childList:true,
            subtree:true,
            attributes:true,
            characterData:true
          });
        }catch(e){}

        // Slow fallback only; this is not the main driver.
        fallbackTimer=setInterval(check,500);
        timeoutTimer=setTimeout(function(){ finish(null); },timeout||4000);
      });
    }

    function txLabelNode(drawer,labelText){
      const wanted=txNorm(labelText).replace(/\s*\*\s*$/,&#x27;&#x27;);
      const nodes=Array.from(drawer.querySelectorAll(&#x27;label,div,span,p&#x27;))
        .filter(txVisible)
        .filter(function(node){
          const own=txNorm(node.textContent||&#x27;&#x27;).replace(/\s*\*\s*$/,&#x27;&#x27;);
          return own===wanted || own.startsWith(wanted);
        });
      if(!nodes.length) return null;
      nodes.sort(function(a,b){
        const ar=a.getBoundingClientRect(), br=b.getBoundingClientRect();
        const aExact=txNorm(a.textContent||&#x27;&#x27;).replace(/\s*\*\s*$/,&#x27;&#x27;)===wanted ? 0 : 1;
        const bExact=txNorm(b.textContent||&#x27;&#x27;).replace(/\s*\*\s*$/,&#x27;&#x27;)===wanted ? 0 : 1;
        if(aExact!==bExact) return aExact-bExact;
        const aArea=ar.width*ar.height, bArea=br.width*br.height;
        return aArea-bArea;
      });
      return nodes[0];
    }

    function txVisibleTextInputs(drawer){
      return Array.from(drawer.querySelectorAll(&#x27;input&#x27;)).filter(function(el){
        return txVisible(el) &amp;&amp; !/radio|checkbox|hidden|button|submit/i.test(el.type||&#x27;&#x27;);
      }).sort(function(a,b){
        return a.getBoundingClientRect().top-b.getBoundingClientRect().top;
      });
    }

    function txClickLikeUser(el){
      if(!el) return false;
      try{ el.scrollIntoView({block:&#x27;center&#x27;,inline:&#x27;nearest&#x27;}); }catch(e){}
      try{ el.focus(); }catch(e){}
      try{
        [&#x27;pointerdown&#x27;,&#x27;mousedown&#x27;,&#x27;pointerup&#x27;,&#x27;mouseup&#x27;,&#x27;click&#x27;].forEach(function(type){
          let evt;
          try{
            evt = type.indexOf(&#x27;pointer&#x27;)===0
              ? new PointerEvent(type,{bubbles:true,cancelable:true,view:window,pointerType:&#x27;mouse&#x27;,isPrimary:true})
              : new MouseEvent(type,{bubbles:true,cancelable:true,view:window});
          }catch(e){
            evt = new Event(type,{bubbles:true,cancelable:true});
          }
          el.dispatchEvent(evt);
        });
        return true;
      }catch(e){
        try{ el.click(); return true; }catch(_e){ return false; }
      }
    }

    function txSetInput(input,value){
      if(!input) return false;
      try{
        input.focus();
        const ok=txNativeSetInput(input,value);
        try{ input.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true,key:&#x27;Tab&#x27;})); }catch(e){}
        try{ input.blur(); }catch(e){}
        return ok;
      }catch(e){
        console.warn(&#x27;[TritoX TM] Dummy insurance input set failed:&#x27;,e);
        return false;
      }
    }

    async function txTypeIntoInput(input,value){
      if(!input) return false;
      try{
        txClickLikeUser(input);
        // Clear through native setter first.
        const desc=Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,&#x27;value&#x27;);
        if(desc&amp;&amp;desc.set) desc.set.call(input,&#x27;&#x27;); else input.value=&#x27;&#x27;;
        try{
          input.dispatchEvent(new InputEvent(&#x27;input&#x27;,{bubbles:true,inputType:&#x27;deleteContentBackward&#x27;}));
        }catch(e){ input.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true})); }
        await new Promise(function(resolve){setTimeout(resolve,100);});

        let current=&#x27;&#x27;;
        for(const ch of String(value)){
          try{ input.dispatchEvent(new KeyboardEvent(&#x27;keydown&#x27;,{bubbles:true,cancelable:true,key:ch})); }catch(e){}
          current+=ch;
          if(desc&amp;&amp;desc.set) desc.set.call(input,current); else input.value=current;
          try{
            input.dispatchEvent(new InputEvent(&#x27;input&#x27;,{
              bubbles:true,cancelable:true,data:ch,inputType:&#x27;insertText&#x27;
            }));
          }catch(e){ input.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true})); }
          try{ input.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true,cancelable:true,key:ch})); }catch(e){}
          await new Promise(function(resolve){setTimeout(resolve,120);});
        }
        input.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
        return txNorm(input.value)===txNorm(value);
      }catch(e){
        return txSetInput(input,value);
      }
    }

    function txFindFieldRow(drawer,labelText){
      const label=txLabelNode(drawer,labelText);
      if(!label) return null;
      const lr=label.getBoundingClientRect();
      const center=lr.top+lr.height/2;

      // Find the smallest ancestor that still looks like one horizontal form row.
      let node=label.parentElement;
      let best=null;
      for(let depth=0;node &amp;&amp; node!==drawer.parentElement &amp;&amp; depth&lt;7;depth++,node=node.parentElement){
        const r=node.getBoundingClientRect();
        const controls=node.querySelectorAll(
          &#x27;input,select,button,[role=&quot;combobox&quot;],[aria-haspopup=&quot;listbox&quot;],[tabindex]&#x27;
        );
        if(controls.length){
          const heightPenalty=r.height&gt;110 ? 500 : 0;
          const score=depth*50+heightPenalty+r.height;
          if(!best || score&lt;best.score) best={root:node,score:score};
        }
      }

      // If ancestry is noisy, create a synthetic row by using the drawer and
      // selecting controls closest to the label&#x27;s Y coordinate.
      return {root:best?best.root:drawer,label:label,labelRect:lr,center:center};
    }

    function txRowControls(drawer,labelText){
      const row=txFindFieldRow(drawer,labelText);
      if(!row) return [];
      const lr=row.labelRect;
      const cy=row.center;
      const candidates=Array.from(drawer.querySelectorAll(
        &#x27;input,select,button,[role=&quot;combobox&quot;],[aria-haspopup=&quot;listbox&quot;],[tabindex]&#x27;
      )).filter(function(el){
        if(!txVisible(el)) return false;
        if(el.closest(&#x27;#tritox-alta-panel&#x27;)) return false;
        if(el.tagName===&#x27;INPUT&#x27; &amp;&amp; /hidden/i.test(el.type||&#x27;&#x27;)) return false;
        const r=el.getBoundingClientRect();
        const ey=r.top+r.height/2;
        return Math.abs(ey-cy)&lt;=55 &amp;&amp; r.right&gt;lr.right-20;
      });

      return candidates.sort(function(a,b){
        const ar=a.getBoundingClientRect(), br=b.getBoundingClientRect();
        const ay=Math.abs((ar.top+ar.height/2)-cy);
        const by=Math.abs((br.top+br.height/2)-cy);
        if(ay!==by) return ay-by;
        // Prefer larger field controls over small icon buttons.
        return (br.width*br.height)-(ar.width*ar.height);
      });
    }

    function txExactVisibleText(text){
      const wanted=txNorm(text);
      const selectors=&#x27;[role=&quot;option&quot;],[role=&quot;menuitem&quot;],mat-option,.mat-option,.dropdown-item,.dropdown-menu li a,.dropdown-menu li button,li,button,a,div,span&#x27;;
      const candidates=Array.from(document.querySelectorAll(selectors)).filter(txVisible).filter(function(el){
        return txNorm(el.textContent||&#x27;&#x27;)===wanted;
      });
      if(!candidates.length) return null;
      // Prefer the smallest exact text node in a popup/overlay.
      candidates.sort(function(a,b){
        const aa=a.getBoundingClientRect(), bb=b.getBoundingClientRect();
        const aOverlay=a.closest(&#x27;[role=&quot;listbox&quot;],[role=&quot;menu&quot;],.cdk-overlay-container,.dropdown-menu,.modal,.drawer&#x27;)?0:1;
        const bOverlay=b.closest(&#x27;[role=&quot;listbox&quot;],[role=&quot;menu&quot;],.cdk-overlay-container,.dropdown-menu,.modal,.drawer&#x27;)?0:1;
        if(aOverlay!==bOverlay) return aOverlay-bOverlay;
        return (aa.width*aa.height)-(bb.width*bb.height);
      });
      return candidates[0];
    }

    async function txOpenAndPick(drawer,labelText,wanted){
      const controls=txRowControls(drawer,labelText);
      let control=controls.find(function(el){
        if(el.tagName===&#x27;INPUT&#x27; &amp;&amp; !/button|submit/i.test(el.type||&#x27;&#x27;)) return false;
        const r=el.getBoundingClientRect();
        return r.width&gt;80 &amp;&amp; (el.tagName===&#x27;SELECT&#x27; ||
          el.getAttribute(&#x27;role&#x27;)===&#x27;combobox&#x27; ||
          el.getAttribute(&#x27;aria-haspopup&#x27;)===&#x27;listbox&#x27; ||
          el.tagName===&#x27;BUTTON&#x27; ||
          el.hasAttribute(&#x27;tabindex&#x27;));
      }) || controls.find(function(el){
        return el.tagName===&#x27;SELECT&#x27; || el.getAttribute(&#x27;role&#x27;)===&#x27;combobox&#x27;;
      });

      if(!control) return false;

      if(control.tagName===&#x27;SELECT&#x27;){
        const wn=txNorm(wanted);
        const opts=Array.from(control.options||[]);
        const opt=opts.find(function(o){return txNorm(o.textContent||o.label||o.value)===wn;}) ||
                  opts.find(function(o){return txNorm(o.textContent||&#x27;&#x27;).includes(wn);});
        if(!opt) return false;
        try{
          const desc=Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,&#x27;value&#x27;);
          if(desc&amp;&amp;desc.set) desc.set.call(control,opt.value); else control.value=opt.value;
          opts.forEach(function(o){o.selected=(o===opt);});
          control.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
          control.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
          control.dispatchEvent(new Event(&#x27;blur&#x27;,{bubbles:true}));
          await new Promise(function(resolve){setTimeout(resolve,120);});
          return txNorm(control.options[control.selectedIndex]&amp;&amp;control.options[control.selectedIndex].textContent||control.value).includes(wn);
        }catch(e){ return false; }
      }

      txClickLikeUser(control);
      let opt=await txWaitFor(function(){ return txExactVisibleText(wanted); },2200);
      if(!opt){
        // If the clickable field itself was not the dropdown trigger, try the
        // widest other control on the same row.
        for(const other of controls){
          if(other===control) continue;
          const r=other.getBoundingClientRect();
          if(r.width&lt;70) continue;
          txClickLikeUser(other);
          opt=await txWaitFor(function(){ return txExactVisibleText(wanted); },900);
          if(opt) break;
        }
      }
      if(!opt) return false;
      txClickLikeUser(opt);
      await new Promise(function(resolve){setTimeout(resolve,45);});
      return true;
    }

    async function txPickAutocomplete(drawer,labelText,value){
      const controls=txRowControls(drawer,labelText);
      let input=controls.find(function(el){
        return el.tagName===&#x27;INPUT&#x27; &amp;&amp; !/radio|checkbox|hidden|button|submit/i.test(el.type||&#x27;&#x27;);
      });
      if(!input){
        const inputs=txVisibleTextInputs(drawer);
        input=inputs[0]||null;
      }
      if(!input) return false;

      await txTypeIntoInput(input,value);
      await new Promise(function(resolve){setTimeout(resolve,350);});

      let opt=txExactVisibleText(value);

      // Force ALTA&#x27;s carrier lookup with the search icon / button on the row.
      if(!opt){
        const searchButton=controls.find(function(el){
          if(el===input) return false;
          const r=el.getBoundingClientRect();
          return (el.tagName===&#x27;BUTTON&#x27; || el.getAttribute(&#x27;role&#x27;)===&#x27;button&#x27; || el.hasAttribute(&#x27;tabindex&#x27;)) &amp;&amp;
                 r.width&lt;=90;
        });
        if(searchButton){
          txClickLikeUser(searchButton);
          opt=await txWaitFor(function(){ return txExactVisibleText(value); },2200);
        }
      }

      if(opt){
        txClickLikeUser(opt);
        await new Promise(function(resolve){setTimeout(resolve,120);});
      }else{
        // Keyboard selection fallback.
        try{
          input.focus();
          input.dispatchEvent(new KeyboardEvent(&#x27;keydown&#x27;,{bubbles:true,cancelable:true,key:&#x27;ArrowDown&#x27;}));
          input.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true,cancelable:true,key:&#x27;ArrowDown&#x27;}));
          await new Promise(function(resolve){setTimeout(resolve,120);});
          input.dispatchEvent(new KeyboardEvent(&#x27;keydown&#x27;,{bubbles:true,cancelable:true,key:&#x27;Enter&#x27;}));
          input.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true,cancelable:true,key:&#x27;Enter&#x27;}));
        }catch(e){}
        await new Promise(function(resolve){setTimeout(resolve,120);});
      }

      // Do not only trust the internal value during the transient autocomplete
      // rerender. Require visible AAA text either in the input or its row.
      const rowText=controls.map(function(el){return cleanText(el.value||el.textContent||&#x27;&#x27;);}).join(&#x27; &#x27;);
      return txNorm(input.value)===txNorm(value) || /\bAAA\b/i.test(rowText);
    }

    async function txFillDate(drawer,value){
      const controls=txRowControls(drawer,&#x27;Policy expiration date&#x27;);
      let input=controls.find(function(el){
        return el.tagName===&#x27;INPUT&#x27; &amp;&amp; !/radio|checkbox|hidden|button|submit/i.test(el.type||&#x27;&#x27;);
      });
      if(!input){
        const inputs=txVisibleTextInputs(drawer);
        input=inputs.find(function(el){ return /date|mm\/dd/i.test((el.type||&#x27;&#x27;)+&#x27; &#x27;+(el.placeholder||&#x27;&#x27;)); }) ||
              inputs[1] || null;
      }
      if(!input) return false;
      txSetInput(input,value);
      await new Promise(function(resolve){setTimeout(resolve,70);});
      try{
        input.focus();
        input.dispatchEvent(new KeyboardEvent(&#x27;keydown&#x27;,{bubbles:true,key:&#x27;Tab&#x27;}));
        input.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true,key:&#x27;Tab&#x27;}));
        input.blur();
      }catch(e){}
      await new Promise(function(resolve){setTimeout(resolve,120);});
      return txNorm(input.value)===txNorm(value);
    }

    async function txChooseContinuousYes(drawer){
      // Click the visible Yes text first. ALTA may use a custom radio control
      // where the real input is hidden.
      const yesNodes=Array.from(drawer.querySelectorAll(&#x27;label,span,div,button&#x27;))
        .filter(txVisible)
        .filter(function(el){ return txNorm(el.textContent||&#x27;&#x27;)===&#x27;yes&#x27;; })
        .sort(function(a,b){
          const ar=a.getBoundingClientRect(), br=b.getBoundingClientRect();
          return (ar.width*ar.height)-(br.width*br.height);
        });
      if(yesNodes.length){
        txClickLikeUser(yesNodes[0]);
        await new Promise(function(resolve){setTimeout(resolve,70);});
      }

      const radios=Array.from(drawer.querySelectorAll(&#x27;input[type=&quot;radio&quot;]&#x27;));
      if(radios.length){
        for(const radio of radios){
          let txt=&#x27;&#x27;;
          try{
            const lab=radio.id ? drawer.querySelector(&#x27;label[for=&quot;&#x27;+CSS.escape(radio.id)+&#x27;&quot;]&#x27;) : null;
            txt=txNorm((lab&amp;&amp;lab.textContent)||(radio.parentElement&amp;&amp;radio.parentElement.textContent)||&#x27;&#x27;);
          }catch(e){}
          if(/\byes\b/.test(txt) &amp;&amp; !/\bno\b/.test(txt)){
            try{radio.checked=true;}catch(e){}
            radio.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
            radio.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
            return true;
          }
        }
        // Fixed current layout: Yes is the first radio.
        try{
          radios[0].checked=true;
          radios[0].dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
          radios[0].dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
        }catch(e){}
        return true;
      }

      // If there are no native radios but Yes was clicked, treat that click as success.
      return yesNodes.length&gt;0;
    }

    function txSixMonthsFromToday(){
      const d=new Date();
      const day=d.getDate();
      d.setDate(1);
      d.setMonth(d.getMonth()+6);
      const lastDay=new Date(d.getFullYear(),d.getMonth()+1,0).getDate();
      d.setDate(Math.min(day,lastDay));
      const mm=String(d.getMonth()+1).padStart(2,&#x27;0&#x27;);
      const dd=String(d.getDate()).padStart(2,&#x27;0&#x27;);
      return mm+&#x27;/&#x27;+dd+&#x27;/&#x27;+d.getFullYear();
    }


    function txDrawerRect(drawer){
      try{ return drawer.getBoundingClientRect(); }
      catch(e){ return {left:0,right:window.innerWidth,top:0,bottom:window.innerHeight,width:window.innerWidth,height:window.innerHeight}; }
    }

    function txPointControl(drawer,labelText){
      const label=txLabelNode(drawer,labelText);
      if(!label) return null;
      const dr=txDrawerRect(drawer);
      const lr=label.getBoundingClientRect();
      const y=Math.round(lr.top+lr.height/2);

      // Probe several X positions across the input area. This works even when
      // ALTA renders a custom div-based control rather than input/select.
      const xs=[
        Math.round(dr.right-90),
        Math.round(dr.right-150),
        Math.round(dr.left+dr.width*0.78),
        Math.round(dr.left+dr.width*0.68)
      ];

      for(const x of xs){
        let el=null;
        try{ el=document.elementFromPoint(x,y); }catch(e){}
        if(!el) continue;
        if(el.closest &amp;&amp; el.closest(&#x27;#tritox-alta-panel&#x27;)) continue;

        // Walk upward until we reach a sensible clickable/editable control.
        let n=el;
        for(let depth=0;n &amp;&amp; n!==drawer &amp;&amp; depth&lt;6;depth++,n=n.parentElement){
          const role=(n.getAttribute&amp;&amp;n.getAttribute(&#x27;role&#x27;))||&#x27;&#x27;;
          const tag=n.tagName||&#x27;&#x27;;
          if(tag===&#x27;INPUT&#x27; || tag===&#x27;SELECT&#x27; || tag===&#x27;TEXTAREA&#x27; ||
             tag===&#x27;BUTTON&#x27; || role===&#x27;combobox&#x27; || role===&#x27;button&#x27; ||
             (n.getAttribute&amp;&amp;n.getAttribute(&#x27;aria-haspopup&#x27;)===&#x27;listbox&#x27;)){
            return n;
          }
        }
        return el;
      }
      return null;
    }

    function txFirstEditableInDrawer(drawer,skip){
      return Array.from(drawer.querySelectorAll(&#x27;input,textarea,[contenteditable=&quot;true&quot;]&#x27;))
        .filter(function(el){
          if(!txVisible(el)) return false;
          if(skip &amp;&amp; skip.includes(el)) return false;
          if(el.tagName===&#x27;INPUT&#x27; &amp;&amp; /radio|checkbox|hidden|button|submit/i.test(el.type||&#x27;&#x27;)) return false;
          return true;
        })
        .sort(function(a,b){
          return a.getBoundingClientRect().top-b.getBoundingClientRect().top;
        })[0] || null;
    }

    async function txHumanFillTextControl(control,value){
      if(!control) return false;

      // If elementFromPoint landed on a wrapper, prefer its editable descendant.
      if(control.tagName!==&#x27;INPUT&#x27; &amp;&amp; control.tagName!==&#x27;TEXTAREA&#x27; &amp;&amp;
         control.getAttribute(&#x27;contenteditable&#x27;)!==&#x27;true&#x27;){
        const child=control.querySelector &amp;&amp; control.querySelector(&#x27;input,textarea,[contenteditable=&quot;true&quot;]&#x27;);
        if(child &amp;&amp; txVisible(child)) control=child;
      }

      txClickLikeUser(control);
      await new Promise(function(resolve){setTimeout(resolve,30);});

      try{
        if(control.tagName===&#x27;INPUT&#x27; || control.tagName===&#x27;TEXTAREA&#x27;){
          control.focus();
          try{ control.select(); }catch(e){}
          // execCommand fires browser-style input events in many Angular controls.
          let usedExec=false;
          try{
            usedExec=document.execCommand &amp;&amp; document.execCommand(&#x27;insertText&#x27;,false,String(value));
          }catch(e){}
          if(!usedExec || txNorm(control.value)!==txNorm(value)){
            txSetInput(control,value);
          }
          control.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
          control.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
          return txNorm(control.value)===txNorm(value);
        }

        if(control.getAttribute(&#x27;contenteditable&#x27;)===&#x27;true&#x27;){
          control.focus();
          control.textContent=&#x27;&#x27;;
          try{ document.execCommand(&#x27;insertText&#x27;,false,String(value)); }
          catch(e){ control.textContent=String(value); }
          control.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
          control.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
          return txNorm(control.textContent)===txNorm(value);
        }
      }catch(e){}
      return false;
    }

    async function txSpatialPick(drawer,labelText,wanted){
      const control=txPointControl(drawer,labelText);
      if(!control) return false;

      // Native select path.
      if(control.tagName===&#x27;SELECT&#x27;){
        const wn=txNorm(wanted);
        const opts=Array.from(control.options||[]);
        const opt=opts.find(function(o){return txNorm(o.textContent||o.label||o.value)===wn;});
        if(!opt) return false;
        try{
          const desc=Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,&#x27;value&#x27;);
          if(desc&amp;&amp;desc.set) desc.set.call(control,opt.value); else control.value=opt.value;
          opts.forEach(function(o){o.selected=(o===opt);});
          control.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
          control.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
          return true;
        }catch(e){}
      }

      txClickLikeUser(control);
      await new Promise(function(resolve){setTimeout(resolve,80);});

      let opt=await txWaitFor(function(){ return txExactVisibleText(wanted); },2200);
      if(!opt){
        // Some ALTA menus are rendered as plain text items without role attributes.
        const wn=txNorm(wanted);
        const all=Array.from(document.querySelectorAll(&#x27;div,span,li,button,a&#x27;))
          .filter(txVisible)
          .filter(function(el){
            if(el.closest &amp;&amp; el.closest(&#x27;#tritox-alta-panel&#x27;)) return false;
            return txNorm(el.textContent||&#x27;&#x27;)===wn;
          })
          .sort(function(a,b){
            const ar=a.getBoundingClientRect(), br=b.getBoundingClientRect();
            return (ar.width*ar.height)-(br.width*br.height);
          });
        opt=all[0]||null;
      }
      if(!opt) return false;
      txClickLikeUser(opt);
      await new Promise(function(resolve){setTimeout(resolve,80);});
      return true;
    }


    function txControlAtRowCenter(drawer,labelText){
      const label=txLabelNode(drawer,labelText);
      if(!label) return null;
      const dr=drawer.getBoundingClientRect();
      const lr=label.getBoundingClientRect();
      const y=Math.round(lr.top+lr.height/2);

      // Target the middle of the field itself, not the search/caret icon.
      const xs=[
        Math.round(dr.left+dr.width*0.68),
        Math.round(dr.left+dr.width*0.73),
        Math.round(dr.left+dr.width*0.63)
      ];

      for(const x of xs){
        let el=null;
        try{ el=document.elementFromPoint(x,y); }catch(e){}
        if(!el || (el.closest&amp;&amp;el.closest(&#x27;#tritox-alta-panel&#x27;))) continue;

        // Prefer a descendant/ancestor that is the actual control.
        if(el.matches&amp;&amp;el.matches(&#x27;input,select,textarea,[role=&quot;combobox&quot;],button,[aria-haspopup=&quot;listbox&quot;]&#x27;)) return el;
        const child=el.querySelector&amp;&amp;el.querySelector(&#x27;input,select,textarea,[role=&quot;combobox&quot;],button,[aria-haspopup=&quot;listbox&quot;]&#x27;);
        if(child&amp;&amp;txVisible(child)) return child;
        const up=el.closest&amp;&amp;el.closest(&#x27;input,select,textarea,[role=&quot;combobox&quot;],button,[aria-haspopup=&quot;listbox&quot;]&#x27;);
        if(up&amp;&amp;txVisible(up)) return up;
        return el;
      }
      return null;
    }

    function txVisibleValueForRow(drawer,labelText){
      const c=txControlAtRowCenter(drawer,labelText);
      if(!c) return &#x27;&#x27;;
      if(c.tagName===&#x27;INPUT&#x27;||c.tagName===&#x27;TEXTAREA&#x27;||c.tagName===&#x27;SELECT&#x27;) return cleanText(c.value||c.textContent||&#x27;&#x27;);
      return cleanText(c.textContent||c.getAttribute(&#x27;aria-valuetext&#x27;)||c.getAttribute(&#x27;aria-label&#x27;)||&#x27;&#x27;);
    }

    async function txPickRowOption(drawer,labelText,wanted){
      let control=txControlAtRowCenter(drawer,labelText);
      if(!control) return false;

      if(control.tagName===&#x27;SELECT&#x27;){
        const wn=txNorm(wanted);
        const opts=Array.from(control.options||[]);
        const opt=opts.find(function(o){return txNorm(o.textContent||o.label||o.value)===wn;});
        if(!opt) return false;
        try{
          const pw=txPageWindow();
          const desc=Object.getOwnPropertyDescriptor(pw.HTMLSelectElement.prototype,&#x27;value&#x27;);
          if(desc&amp;&amp;desc.set) desc.set.call(control,opt.value); else control.value=opt.value;
          opts.forEach(function(o){o.selected=(o===opt);});
          control.dispatchEvent(new pw.Event(&#x27;input&#x27;,{bubbles:true}));
          control.dispatchEvent(new pw.Event(&#x27;change&#x27;,{bubbles:true}));
          await new Promise(function(resolve){setTimeout(resolve,180);});
          return true;
        }catch(e){ return false; }
      }

      txClickLikeUser(control);
      await new Promise(function(resolve){setTimeout(resolve,120);});

      const wn=txNorm(wanted);
      let option=await txWaitFor(function(){
        const candidates=Array.from(document.querySelectorAll(
          &#x27;[role=&quot;option&quot;],[role=&quot;menuitem&quot;],mat-option,.mat-option,.dropdown-item,.dropdown-menu li a,.dropdown-menu li button,li,button,a,div,span&#x27;
        )).filter(txVisible).filter(function(el){
          if(el.closest&amp;&amp;el.closest(&#x27;#tritox-alta-panel&#x27;)) return false;
          const t=txNorm(el.textContent||&#x27;&#x27;);
          return t===wn;
        });
        if(!candidates.length) return null;
        candidates.sort(function(a,b){
          const ar=a.getBoundingClientRect(), br=b.getBoundingClientRect();
          return (ar.width*ar.height)-(br.width*br.height);
        });
        return candidates[0];
      },2500);

      if(!option) return false;
      txClickLikeUser(option);
      await new Promise(function(resolve){setTimeout(resolve,120);});
      return true;
    }

    async function txFillCarrierDirect(drawer,value){
      let input=txControlAtRowCenter(drawer,&#x27;Current insurance company&#x27;);
      if(input &amp;&amp; input.tagName!==&#x27;INPUT&#x27;){
        const child=input.querySelector&amp;&amp;input.querySelector(&#x27;input&#x27;);
        if(child&amp;&amp;txVisible(child)) input=child;
      }
      if(!input || input.tagName!==&#x27;INPUT&#x27;){
        input=txVisibleTextInputs(drawer)[0]||null;
      }
      if(!input) return false;

      txClickLikeUser(input);
      await new Promise(function(resolve){setTimeout(resolve,30);});
      txNativeSetInput(input,&#x27;&#x27;);
      await new Promise(function(resolve){setTimeout(resolve,80);});
      txNativeSetInput(input,value);
      await new Promise(function(resolve){setTimeout(resolve,280);});

      // Click the lookup icon on the same row if present.
      const row=txFindFieldRow(drawer,&#x27;Current insurance company&#x27;);
      if(row){
        const cy=row.center;
        const lr=row.labelRect;
        const search=Array.from(drawer.querySelectorAll(&#x27;button,[role=&quot;button&quot;],[tabindex],svg&#x27;))
          .filter(txVisible)
          .find(function(el){
            const r=el.getBoundingClientRect();
            return Math.abs((r.top+r.height/2)-cy)&lt;45 &amp;&amp; r.left&gt;lr.right &amp;&amp; r.width&lt;80;
          });
        if(search){
          txClickLikeUser(search);
          await new Promise(function(resolve){setTimeout(resolve,300);});
        }
      }

      // Select exact AAA option if ALTA opens a lookup/autocomplete list.
      const wn=txNorm(value);
      const option=await txWaitFor(function(){
        const candidates=Array.from(document.querySelectorAll(
          &#x27;[role=&quot;option&quot;],[role=&quot;menuitem&quot;],mat-option,.mat-option,.dropdown-item,.dropdown-menu li a,.dropdown-menu li button,li,button,a,div,span&#x27;
        )).filter(txVisible).filter(function(el){
          if(el.closest&amp;&amp;el.closest(&#x27;#tritox-alta-panel&#x27;)) return false;
          return txNorm(el.textContent||&#x27;&#x27;)===wn;
        });
        if(!candidates.length) return null;
        candidates.sort(function(a,b){
          const ar=a.getBoundingClientRect(), br=b.getBoundingClientRect();
          return (ar.width*ar.height)-(br.width*br.height);
        });
        return candidates[0];
      },1400);

      if(option){
        txClickLikeUser(option);
        await new Promise(function(resolve){setTimeout(resolve,90);});
      }

      // Final page-world value refresh in case selecting the lookup rerendered input.
      const fresh=txControlAtRowCenter(drawer,&#x27;Current insurance company&#x27;);
      const finalInput=(fresh&amp;&amp;fresh.tagName===&#x27;INPUT&#x27;)?fresh:(txVisibleTextInputs(drawer)[0]||input);
      if(finalInput &amp;&amp; txNorm(finalInput.value)!==wn){
        txNativeSetInput(finalInput,value);
        await new Promise(function(resolve){setTimeout(resolve,120);});
      }
      return finalInput &amp;&amp; txNorm(finalInput.value)===wn;
    }

    async function txFillDateDirect(drawer,value){
      let input=txControlAtRowCenter(drawer,&#x27;Policy expiration date&#x27;);
      if(input &amp;&amp; input.tagName!==&#x27;INPUT&#x27;){
        const child=input.querySelector&amp;&amp;input.querySelector(&#x27;input&#x27;);
        if(child&amp;&amp;txVisible(child)) input=child;
      }
      if(!input || input.tagName!==&#x27;INPUT&#x27;){
        const inputs=txVisibleTextInputs(drawer);
        input=inputs.find(function(el){return /date|mm\/dd/i.test((el.type||&#x27;&#x27;)+&#x27; &#x27;+(el.placeholder||&#x27;&#x27;));}) || inputs[1] || null;
      }
      if(!input) return false;

      let internal=value;
      if((input.type||&#x27;&#x27;).toLowerCase()===&#x27;date&#x27;){
        const m=String(value).match(/^(\d{2})\/(\d{2})\/(\d{4})$/);
        if(m) internal=m[3]+&#x27;-&#x27;+m[1]+&#x27;-&#x27;+m[2];
      }
      txClickLikeUser(input);
      txNativeSetInput(input,internal);
      await new Promise(function(resolve){setTimeout(resolve,180);});
      return !!input.value;
    }

    async function txSpatialCarrier(drawer,value){
      let control=txPointControl(drawer,&#x27;Current insurance company&#x27;);
      if(!control || (control.tagName!==&#x27;INPUT&#x27; &amp;&amp; control.tagName!==&#x27;TEXTAREA&#x27;)){
        const inputs=txVisibleTextInputs(drawer);
        control=inputs[0]||control;
      }
      if(!control) return false;

      await txHumanFillTextControl(control,value);
      await new Promise(function(resolve){setTimeout(resolve,120);});

      // Click search icon if present on the same row.
      const row=txFindFieldRow(drawer,&#x27;Current insurance company&#x27;);
      if(row){
        const lr=row.labelRect;
        const cy=row.center;
        const search=Array.from(drawer.querySelectorAll(&#x27;button,[role=&quot;button&quot;],[tabindex]&#x27;))
          .filter(txVisible)
          .find(function(el){
            const r=el.getBoundingClientRect();
            return Math.abs((r.top+r.height/2)-cy)&lt;45 &amp;&amp; r.left&gt;lr.right &amp;&amp; r.width&lt;90;
          });
        if(search){
          txClickLikeUser(search);
          await new Promise(function(resolve){setTimeout(resolve,250);});
        }
      }

      let opt=await txWaitFor(function(){ return txExactVisibleText(value); },1100);
      if(opt){
        txClickLikeUser(opt);
        await new Promise(function(resolve){setTimeout(resolve,180);});
      }else{
        // Keyboard selection fallback after the lookup has been triggered.
        try{
          control.focus();
          control.dispatchEvent(new KeyboardEvent(&#x27;keydown&#x27;,{bubbles:true,key:&#x27;ArrowDown&#x27;}));
          control.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true,key:&#x27;ArrowDown&#x27;}));
          await new Promise(function(resolve){setTimeout(resolve,100);});
          control.dispatchEvent(new KeyboardEvent(&#x27;keydown&#x27;,{bubbles:true,key:&#x27;Enter&#x27;}));
          control.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true,key:&#x27;Enter&#x27;}));
        }catch(e){}
      }

      await new Promise(function(resolve){setTimeout(resolve,90);});
      return true;
    }


    // Exact selectors from the current ALTA In-force policy drawer.
    function txInsuranceRow(drawer,labelText){
      const wanted=txNorm(labelText);

      // Normal rows used by BI / tenure.
      const direct=Array.from(drawer.querySelectorAll(&#x27;.prior-insurance-bi-limit&#x27;))
        .find(function(row){
          const title=row.querySelector(&#x27;.prior-insurance-bi-limit-title&#x27;);
          return title &amp;&amp; txNorm(title.textContent||&#x27;&#x27;).startsWith(wanted);
        });
      if(direct) return direct;

      // Current insurance company uses a different wrapper in the latest ALTA
      // layout. Find its title first, then climb until the matching input/button
      // is inside the same visual row.
      const title=Array.from(drawer.querySelectorAll(&#x27;.prior-insurance-bi-limit-title,div,span,label&#x27;))
        .filter(txVisible)
        .find(function(el){
          return txNorm(el.textContent||&#x27;&#x27;).startsWith(wanted);
        });
      if(!title) return null;

      let p=title.parentElement;
      for(let depth=0;p &amp;&amp; p!==drawer.parentElement &amp;&amp; depth&lt;7;depth++,p=p.parentElement){
        if(p.querySelector &amp;&amp; p.querySelector(&#x27;input,button,[role=&quot;button&quot;],mat-icon&#x27;)){
          return p;
        }
      }
      return title.parentElement || null;
    }

    function txCompanyInputExact(drawer){
      // Exact ALTA structure supplied from DevTools:
      // &lt;input class=&quot;mat-mdc-autocomplete-trigger ...&quot; role=&quot;combobox&quot; ...&gt;
      // inside a mat-form-field whose mat-label is &quot;Current insurance company&quot;.
      const inputs=Array.from(drawer.querySelectorAll(
        &#x27;input.mat-mdc-autocomplete-trigger[role=&quot;combobox&quot;], input[role=&quot;combobox&quot;][aria-autocomplete=&quot;list&quot;]&#x27;
      )).filter(txVisible);

      for(const input of inputs){
        const form=input.closest(&#x27;mat-form-field&#x27;);
        const label=form &amp;&amp; form.querySelector(&#x27;mat-label&#x27;);
        if(label &amp;&amp; /current insurance company/i.test(cleanText(label.textContent||&#x27;&#x27;))){
          return input;
        }
      }

      // Fallback if ALTA changes generated Angular classes/IDs.
      return inputs[0] || null;
    }

    function txExactSelectedText(control){
      if(!control) return &#x27;&#x27;;
      return cleanText(
        (control.querySelector &amp;&amp; control.querySelector(&#x27;.mat-mdc-select-value-text&#x27;) &amp;&amp;
          control.querySelector(&#x27;.mat-mdc-select-value-text&#x27;).textContent) ||
        control.textContent || &#x27;&#x27;
      );
    }

    async function txPickMatSelectExact(selectId,wanted){
      const control=document.getElementById(selectId);
      if(!control || !txVisible(control)) return false;

      txClickLikeUser(control);

      const option=await txWaitFor(function(){
        const wn=txNorm(wanted);
        const opts=Array.from(document.querySelectorAll(&#x27;mat-option,[role=&quot;option&quot;]&#x27;))
          .filter(txVisible);
        return opts.find(function(el){
          return txNorm(el.textContent||&#x27;&#x27;)===wn;
        }) || null;
      },3000);

      if(!option) return false;
      txClickLikeUser(option);

      const selected=await txWaitFor(function(){
        const text=txNorm(txExactSelectedText(control));
        return (text===txNorm(wanted) || text.includes(txNorm(wanted))) ? control : null;
      },1500);

      return !!selected;
    }

    async function txFillPolicyExpiryExact(value){
      const input=document.querySelector(
        &#x27;mat-form-field#policyExpiryDate input#policyExpiryDate[type=&quot;tel&quot;], input#policyExpiryDate[type=&quot;tel&quot;]&#x27;
      );
      if(!input || !txVisible(input)) return false;

      const m=String(value).match(/^(\d{2})\/(\d{2})\/(\d{4})$/);
      const digits=m ? (m[1]+m[2]+m[3]) : String(value).replace(/\D/g,&#x27;&#x27;);
      const pw=txPageWindow();

      try{
        const desc=Object.getOwnPropertyDescriptor(pw.HTMLInputElement.prototype,&#x27;value&#x27;);
        try{ input.focus(); }catch(e){}
        try{ input.dispatchEvent(new pw.FocusEvent(&#x27;focusin&#x27;,{bubbles:true,composed:true})); }catch(e){}

        // First try the whole masked date in a single Angular input event.
        if(desc&amp;&amp;desc.set) desc.set.call(input,digits); else input.value=digits;
        try{
          input.dispatchEvent(new pw.InputEvent(&#x27;input&#x27;,{
            bubbles:true,composed:true,data:digits,inputType:&#x27;insertText&#x27;
          }));
        }catch(e){
          input.dispatchEvent(new pw.Event(&#x27;input&#x27;,{bubbles:true,composed:true}));
        }
        input.dispatchEvent(new pw.Event(&#x27;change&#x27;,{bubbles:true,composed:true}));

        let ok=await txWaitFor(function(){
          return String(input.value||&#x27;&#x27;).trim() ? input : null;
        },500);

        // Fallback: fire the progressive Angular events synchronously, with no
        // tiny timers that would be clamped in a background Chrome tab.
        if(!ok){
          let typed=&#x27;&#x27;;
          if(desc&amp;&amp;desc.set) desc.set.call(input,&#x27;&#x27;); else input.value=&#x27;&#x27;;
          for(const ch of digits){
            typed+=ch;
            if(desc&amp;&amp;desc.set) desc.set.call(input,typed); else input.value=typed;
            try{
              input.dispatchEvent(new pw.InputEvent(&#x27;input&#x27;,{
                bubbles:true,composed:true,data:ch,inputType:&#x27;insertText&#x27;
              }));
            }catch(e){
              input.dispatchEvent(new pw.Event(&#x27;input&#x27;,{bubbles:true,composed:true}));
            }
            try{
              input.dispatchEvent(new pw.KeyboardEvent(&#x27;keyup&#x27;,{
                bubbles:true,composed:true,key:ch
              }));
            }catch(e){}
          }
          input.dispatchEvent(new pw.Event(&#x27;change&#x27;,{bubbles:true,composed:true}));
          ok=await txWaitFor(function(){
            return String(input.value||&#x27;&#x27;).trim() ? input : null;
          },600);
        }

        return !!ok;
      }catch(e){
        console.warn(&#x27;[TritoX TM] exact policy expiry fill failed&#x27;,e);
        return false;
      }
    }

    async function txFillCompanyExact(drawer,value){
      const pw=txPageWindow();
      const pd=pw.document;
      const wanted=txNorm(value);

      function findInput(){
        const inputs=Array.from(pd.querySelectorAll(
          &#x27;input.mat-mdc-autocomplete-trigger[role=&quot;combobox&quot;], input[role=&quot;combobox&quot;][aria-autocomplete=&quot;list&quot;]&#x27;
        )).filter(function(el){
          try{
            const r=el.getBoundingClientRect();
            return r.width&gt;0 &amp;&amp; r.height&gt;0;
          }catch(e){ return false; }
        });

        for(const input of inputs){
          const form=input.closest &amp;&amp; input.closest(&#x27;mat-form-field&#x27;);
          const label=form &amp;&amp; form.querySelector(&#x27;mat-label&#x27;);
          if(label &amp;&amp; /current insurance company/i.test(String(label.textContent||&#x27;&#x27;))){
            return input;
          }
        }
        return inputs[0] || null;
      }

      function findExactOption(){
        const roots=Array.from(pd.querySelectorAll(
          &#x27;.cdk-overlay-pane,.mat-mdc-autocomplete-panel,[role=&quot;listbox&quot;]&#x27;
        )).filter(function(el){
          try{
            const r=el.getBoundingClientRect();
            return r.width&gt;0 &amp;&amp; r.height&gt;0;
          }catch(e){ return false; }
        });

        for(const root of roots){
          const opts=Array.from(root.querySelectorAll(
            &#x27;mat-option,[role=&quot;option&quot;],.mat-mdc-option,.mat-option&#x27;
          )).filter(function(el){
            try{
              const r=el.getBoundingClientRect();
              return r.width&gt;0 &amp;&amp; r.height&gt;0;
            }catch(e){ return false; }
          });

          let opt=opts.find(function(el){
            return txNorm(el.textContent||&#x27;&#x27;)===wanted;
          });
          if(opt) return opt;

          opt=opts.find(function(el){
            const t=txNorm(el.textContent||&#x27;&#x27;);
            return t.startsWith(wanted+&#x27; &#x27;) || t.startsWith(wanted+&#x27;-&#x27;);
          });
          if(opt) return opt;
        }
        return null;
      }

      function clickPage(el){
        if(!el) return;
        try{
          el.dispatchEvent(new pw.MouseEvent(&#x27;mousedown&#x27;,{bubbles:true,cancelable:true,view:pw}));
          el.dispatchEvent(new pw.MouseEvent(&#x27;mouseup&#x27;,{bubbles:true,cancelable:true,view:pw}));
          el.dispatchEvent(new pw.MouseEvent(&#x27;click&#x27;,{bubbles:true,cancelable:true,view:pw}));
        }catch(e){
          try{ pw.HTMLElement.prototype.click.call(el); }
          catch(_e){ try{ el.click(); }catch(__e){} }
        }
      }

      function setPageValue(input,text){
        const desc=Object.getOwnPropertyDescriptor(pw.HTMLInputElement.prototype,&#x27;value&#x27;);
        if(desc &amp;&amp; desc.set) desc.set.call(input,String(text));
        else input.value=String(text);

        try{
          input.dispatchEvent(new pw.InputEvent(&#x27;input&#x27;,{
            bubbles:true,
            composed:true,
            cancelable:false,
            data:String(text),
            inputType:&#x27;insertText&#x27;
          }));
        }catch(e){
          input.dispatchEvent(new pw.Event(&#x27;input&#x27;,{bubbles:true,composed:true}));
        }
        input.dispatchEvent(new pw.Event(&#x27;change&#x27;,{bubbles:true,composed:true}));
      }

      function isSelected(input){
        if(!input) return false;
        const form=input.closest &amp;&amp; input.closest(&#x27;mat-form-field&#x27;);
        const invalid=
          input.classList.contains(&#x27;ng-invalid&#x27;) ||
          input.getAttribute(&#x27;aria-invalid&#x27;)===&#x27;true&#x27; ||
          !!(form &amp;&amp; (
            form.classList.contains(&#x27;mat-form-field-invalid&#x27;) ||
            form.classList.contains(&#x27;mat-mdc-form-field-invalid&#x27;)
          ));
        return txNorm(input.value||&#x27;&#x27;)===wanted &amp;&amp; !invalid;
      }

      let input=findInput();
      if(!input) return false;

      // Use ALTA&#x27;s page realm and event-driven DOM waits so this continues even
      // after the user switches to another Chrome tab.
      try{ input.focus(); }catch(e){}
      try{ input.dispatchEvent(new pw.FocusEvent(&#x27;focusin&#x27;,{bubbles:true,composed:true})); }catch(e){}
      setPageValue(input,&#x27;&#x27;);
      setPageValue(input,value);

      if(input.getAttribute(&#x27;aria-expanded&#x27;)!==&#x27;true&#x27;){
        clickPage(input);
      }

      let option=await txWaitFor(function(){
        input=findInput()||input;
        return findExactOption();
      },1800);

      if(option){
        clickPage(option);

        // Wait for ALTA to commit the selected autocomplete value instead of a
        // fixed foreground-only sleep.
        await txWaitFor(function(){
          input=findInput()||input;
          return txNorm(input.value||&#x27;&#x27;)===wanted ? input : null;
        },900);
      }else{
        // Final Material fallback: when the panel is open, ArrowDown + Enter
        // selects the first autocomplete result.
        try{
          input.focus();
          input.dispatchEvent(new pw.KeyboardEvent(&#x27;keydown&#x27;,{
            bubbles:true,composed:true,cancelable:true,key:&#x27;ArrowDown&#x27;,code:&#x27;ArrowDown&#x27;
          }));
          input.dispatchEvent(new pw.KeyboardEvent(&#x27;keyup&#x27;,{
            bubbles:true,composed:true,cancelable:true,key:&#x27;ArrowDown&#x27;,code:&#x27;ArrowDown&#x27;
          }));
          await new Promise(function(resolve){setTimeout(resolve,180);});
          input.dispatchEvent(new pw.KeyboardEvent(&#x27;keydown&#x27;,{
            bubbles:true,composed:true,cancelable:true,key:&#x27;Enter&#x27;,code:&#x27;Enter&#x27;
          }));
          input.dispatchEvent(new pw.KeyboardEvent(&#x27;keyup&#x27;,{
            bubbles:true,composed:true,cancelable:true,key:&#x27;Enter&#x27;,code:&#x27;Enter&#x27;
          }));
          await new Promise(function(resolve){setTimeout(resolve,100);});
        }catch(e){}
      }

      input=findInput()||input;

      // If AAA is visibly present, allow the workflow to continue to Save.
      // ALTA sometimes keeps the Angular invalid class for a short time even
      // after the visible value is filled. The Save click is the final validator.
      const strictOk=isSelected(input);
      const visibleOk=txNorm(input &amp;&amp; input.value||&#x27;&#x27;)===wanted;
      const ok=strictOk || visibleOk;
      if(strictOk){
        try{ input.blur(); }catch(e){}
      }

      console.log(&#x27;[TritoX TM] page-realm company result&#x27;,{
        value:input &amp;&amp; input.value,
        ariaExpanded:input &amp;&amp; input.getAttribute(&#x27;aria-expanded&#x27;),
        ariaControls:input &amp;&amp; input.getAttribute(&#x27;aria-controls&#x27;),
        ariaInvalid:input &amp;&amp; input.getAttribute(&#x27;aria-invalid&#x27;),
        className:input &amp;&amp; input.className,
        optionFound:!!option,
        ok:ok
      });
      return ok;
    }

    function txExactInsuranceValues(drawer){
      const company=txCompanyInputExact(drawer);
      const bi=document.getElementById(&#x27;auto-add-driver-policyBILimits__input-section&#x27;);
      const date=document.querySelector(
        &#x27;mat-form-field#policyExpiryDate input#policyExpiryDate[type=&quot;tel&quot;], input#policyExpiryDate[type=&quot;tel&quot;]&#x27;
      );
      const tenure=document.getElementById(&#x27;auto-add-driver-insuranceTenure__input-section&#x27;);
      return {
        company:company ? cleanText(company.value||&#x27;&#x27;) : &#x27;&#x27;,
        bi:txExactSelectedText(bi),
        date:date ? cleanText(date.value||&#x27;&#x27;) : &#x27;&#x27;,
        tenure:txExactSelectedText(tenure)
      };
    }

    async function addDummyInsurance(){
      const status=document.getElementById(&#x27;tx-alta-status&#x27;);
      function say(msg,color){
        if(status){ status.textContent=msg; status.style.color=color||&#x27;#64748b&#x27;; }
      }

      const prior=priorInsuranceSnapshot();
      if(prior.visible &amp;&amp; prior.inEffect){
        say(&#x27;In Effect already exists&#x27;,&#x27;#15803d&#x27;);
        setTimeout(function(){
          if(status){status.textContent=&#x27;Auto-saved&#x27;;status.style.color=&#x27;#64748b&#x27;;}
        },1800);
        return;
      }

      say(document.hidden?&#x27;Opening insurance in background…&#x27;:&#x27;Opening insurance…&#x27;,&#x27;#2563eb&#x27;);

      const addBtn=txFindButton(/add\s+in-force/i,document);
      if(!addBtn){
        say(&#x27;Open Drivers &amp; vehicles&#x27;,&#x27;#b45309&#x27;);
        return;
      }

      txClickLikeUser(addBtn);

      let drawer=await txWaitFor(txFindDrawer,5000);
      if(!drawer){
        say(&#x27;Insurance form not found&#x27;,&#x27;#b91c1c&#x27;);
        return;
      }

      // ALTA mounts this drawer in stages. Wait for the four required rows.
      const controlsReady=await txWaitFor(function(){
        drawer=txFindDrawer()||drawer;
        const txt=cleanText(drawer.innerText||&#x27;&#x27;);
        const required=
          /Current insurance company/i.test(txt) &amp;&amp;
          /Current BI limits/i.test(txt) &amp;&amp;
          /Policy expiration date/i.test(txt) &amp;&amp;
          /How long were they insured/i.test(txt);
        return required ? drawer : null;
      },3500);
      if(controlsReady) drawer=controlsReady;

      say(&#x27;Filling insurance…&#x27;,&#x27;#2563eb&#x27;);

      const expiration=txSixMonthsFromToday();

      // ONE PASS ONLY:
      // Company -&gt; BI -&gt; Date -&gt; Tenure -&gt; Save.
      // Do not return to the company field after tenure.
      const companyOk=await txFillCompanyExact(drawer,&#x27;AAA&#x27;);

      const biOk=await txPickMatSelectExact(
        &#x27;auto-add-driver-policyBILimits__input-section&#x27;,
        &#x27;$100,000/$300,000&#x27;
      );

      const dateOk=await txFillPolicyExpiryExact(expiration);

      const tenureOk=await txPickMatSelectExact(
        &#x27;auto-add-driver-insuranceTenure__input-section&#x27;,
        &#x27;6 - 11 Months&#x27;
      );

      // Tenure causes ALTA to auto-select Yes. Wait for that Angular state via a
      // DOM mutation, not a short timer that Chrome may throttle in background.
      await txWaitFor(function(){
        const radios=Array.from(drawer.querySelectorAll(&#x27;input[type=&quot;radio&quot;]&#x27;));
        return radios.find(function(r){ return r.checked; }) || null;
      },1200);

      // All four controls are now visibly filled in ALTA. Do not re-read the
      // Angular FormControl state here: ALTA can briefly report the company as
      // empty even while AAA is visibly selected. Go straight to Save.
      console.log(&#x27;[TritoX TM] insurance fill finished&#x27;,{
        companyOk:companyOk,biOk:biOk,dateOk:dateOk,tenureOk:tenureOk
      });

      say(document.hidden?&#x27;Saving insurance in background…&#x27;:&#x27;Saving insurance…&#x27;,&#x27;#2563eb&#x27;);

      const pw=txPageWindow();
      let saveBtn=
        pw.document.querySelector(&#x27;button.auto-add-driver-save__button&#x27;) ||
        document.querySelector(&#x27;button.auto-add-driver-save__button&#x27;);

      if(!saveBtn){
        say(&#x27;Insurance Save not found&#x27;,&#x27;#b91c1c&#x27;);
        return;
      }

      function clickSaveDirect(btn){
        if(!btn) return false;
        try{
          btn.dispatchEvent(new pw.MouseEvent(&#x27;mousedown&#x27;,{bubbles:true,cancelable:true,view:pw}));
          btn.dispatchEvent(new pw.MouseEvent(&#x27;mouseup&#x27;,{bubbles:true,cancelable:true,view:pw}));
          btn.dispatchEvent(new pw.MouseEvent(&#x27;click&#x27;,{bubbles:true,cancelable:true,view:pw}));
          return true;
        }catch(e){
          try{
            pw.HTMLElement.prototype.click.call(btn);
            return true;
          }catch(_e){
            try{ btn.click(); return true; }catch(__e){ return false; }
          }
        }
      }

      clickSaveDirect(saveBtn);

      let finalClosed=await txWaitFor(function(){
        const d=txFindDrawer();
        return !d || !txVisible(d) ? true : null;
      },650);

      // If ALTA needs a moment to enable/commit the form, retry Save only.
      // Never touch Company / BI / Date / Tenure again.
      if(!finalClosed){
        saveBtn=
          pw.document.querySelector(&#x27;button.auto-add-driver-save__button&#x27;) ||
          document.querySelector(&#x27;button.auto-add-driver-save__button&#x27;);
        clickSaveDirect(saveBtn);

        finalClosed=await txWaitFor(function(){
          const d=txFindDrawer();
          return !d || !txVisible(d) ? true : null;
        },1800);
      }

      if(!finalClosed){
        say(&#x27;Fields filled — Save did not close drawer&#x27;,&#x27;#b91c1c&#x27;);
        return;
      }

      say(&#x27;Insurance added ✓&#x27;,&#x27;#15803d&#x27;);
      setTimeout(function(){
        try{ buildPanel(); }catch(e){}
        const s=document.getElementById(&#x27;tx-alta-status&#x27;);
        if(s){s.textContent=&#x27;Auto-saved&#x27;;s.style.color=&#x27;#64748b&#x27;;}
      },900);
    }

    function buildPanel(){
      const ident=altaIdentity();
      if(!ident.id || !ident.name) return;
      let panel=document.getElementById(&#x27;tritox-alta-panel&#x27;);
      const existing=readSaved(ident.id);
      const prior=priorInsuranceSnapshot();
      const detected={
        company:prior.inEffect?prior.company:&#x27;&#x27;,
        renewalDate:prior.inEffect?prior.renewalDate:&#x27;&#x27;,
        star:detectStar()
      };
      // If the Prior insurance section is visible and is not In Effect, clear
      // carrier/date for this lead. On other ALTA pages preserve the last valid
      // In Effect values captured earlier in the same 2-hour window.
      if(prior.visible &amp;&amp; !prior.inEffect){
        existing.company=&#x27;&#x27;;
        existing.renewalDate=&#x27;&#x27;;
        existing.companyInEffect=false;
      }
      const state={
        name:ident.name,
        altaId:ident.id,
        company:(prior.inEffect?detected.company:(existing.companyInEffect?existing.company:&#x27;&#x27;))||&#x27;&#x27;,
        renewalDate:(prior.inEffect?detected.renewalDate:(existing.companyInEffect?existing.renewalDate:&#x27;&#x27;))||&#x27;&#x27;,
        // Keep blank until ALTA&#x27;s Auto coverages page exposes the actual star.
        // On that page, the detected value wins so an old/manual value cannot
        // survive when ALTA clearly shows a different rating.
        star:onAutoCoverageSection() ? (detected.star||&#x27;&#x27;) : (existing.star||&#x27;&#x27;),
        companyInEffect:prior.inEffect?true:!!existing.companyInEffect
      };

      if(!panel){
        panel=document.createElement(&#x27;div&#x27;);
        panel.id=&#x27;tritox-alta-panel&#x27;;
        panel.style.cssText=&#x27;position:fixed;right:20px;bottom:20px;z-index:2147483646;width:315px;background:#fff;border:2px solid #17243b;border-radius:12px;box-shadow:0 8px 28px rgba(0,0,0,.18);padding:14px;font-family:Arial,sans-serif;color:#17243b;&#x27;;
        panel.innerHTML=&#x27;&#x27;
          +&#x27;&lt;div id=&quot;tx-alta-head&quot; style=&quot;display:flex;justify-content:space-between;align-items:center;margin-bottom:10px;gap:8px;&quot;&gt;&#x27;
          +&#x27;&lt;strong style=&quot;font-size:14px;white-space:nowrap;&quot;&gt;TritoX Lead Info&lt;/strong&gt;&#x27;
          +&#x27;&lt;div style=&quot;display:flex;align-items:center;gap:7px;&quot;&gt;&#x27;
          +&#x27;&lt;span id=&quot;tx-alta-status&quot; style=&quot;font-size:11px;color:#64748b;white-space:nowrap;&quot;&gt;Auto-saved&lt;/span&gt;&#x27;
          +&#x27;&lt;button id=&quot;tx-alta-minimize&quot; type=&quot;button&quot; title=&quot;Minimize&quot; aria-label=&quot;Minimize TritoX Lead Info&quot; style=&quot;width:25px;height:25px;border:1px solid #cbd5e1;border-radius:6px;background:#f8fafc;color:#17243b;font-size:18px;line-height:20px;font-weight:700;cursor:pointer;padding:0;&quot;&gt;−&lt;/button&gt;&#x27;
          +&#x27;&lt;/div&gt;&lt;/div&gt;&#x27;
          +&#x27;&lt;div id=&quot;tx-alta-body&quot;&gt;&#x27;
          +&#x27;&lt;div id=&quot;tx-alta-ident&quot; style=&quot;font-size:11px;color:#64748b;margin-bottom:10px;line-height:1.45;&quot;&gt;&lt;/div&gt;&#x27;
          +&#x27;&lt;div id=&quot;tx-alta-pip-driver&quot; style=&quot;display:none;margin:0 0 10px;padding:9px 10px;border:1px solid #86efac;background:#f0fdf4;border-radius:8px;font-size:11px;line-height:1.45;color:#166534;&quot;&gt;&lt;/div&gt;&#x27;
          +&#x27;&lt;label style=&quot;display:block;font-size:11px;font-weight:700;margin:7px 0 4px;&quot;&gt;Current Company&lt;/label&gt;&#x27;
          +&#x27;&lt;input id=&quot;tx-alta-company&quot; list=&quot;tx-carriers&quot; placeholder=&quot;Select or type carrier&quot; style=&quot;width:100%;height:34px;border:1px solid #cbd5e1;border-radius:7px;padding:0 9px;font-size:12px;&quot;&gt;&#x27;
          +&#x27;&lt;datalist id=&quot;tx-carriers&quot;&gt;&#x27;+CARRIERS.map(function(c){return &#x27;&lt;option value=&quot;&#x27;+c.replace(/&quot;/g,&#x27;&amp;quot;&#x27;)+&#x27;&quot;&gt;&lt;/option&gt;&#x27;;}).join(&#x27;&#x27;)+&#x27;&lt;/datalist&gt;&#x27;
          +&#x27;&lt;label style=&quot;display:block;font-size:11px;font-weight:700;margin:9px 0 4px;&quot;&gt;Auto Renewal Date&lt;/label&gt;&#x27;
          +&#x27;&lt;input id=&quot;tx-alta-date&quot; type=&quot;text&quot; placeholder=&quot;MM/DD/YYYY&quot; style=&quot;width:100%;height:34px;border:1px solid #cbd5e1;border-radius:7px;padding:0 9px;font-size:12px;&quot;&gt;&#x27;
          +&#x27;&lt;label style=&quot;display:block;font-size:11px;font-weight:700;margin:9px 0 4px;&quot;&gt;Star / BW&lt;/label&gt;&#x27;
          +&#x27;&lt;select id=&quot;tx-alta-star&quot; style=&quot;width:100%;height:34px;border:1px solid #cbd5e1;border-radius:7px;padding:0 8px;font-size:12px;background:#fff;&quot;&gt;&#x27;
          +&#x27;&lt;option value=&quot;&quot;&gt;Select&lt;/option&gt;&lt;option value=&quot;1&quot;&gt;1 Star&lt;/option&gt;&lt;option value=&quot;2&quot;&gt;2 Stars&lt;/option&gt;&lt;option value=&quot;3&quot;&gt;3 Stars&lt;/option&gt;&lt;option value=&quot;BW&quot;&gt;BW&lt;/option&gt;&lt;/select&gt;&#x27;
          +&#x27;&lt;button id=&quot;tx-alta-save&quot; style=&quot;width:100%;margin-top:11px;height:36px;border:0;border-radius:8px;background:#2563eb;color:white;font-weight:700;cursor:pointer;&quot;&gt;Save Lead Info&lt;/button&gt;&#x27;
          +&#x27;&lt;button id=&quot;tx-alta-add-insurance&quot; style=&quot;width:100%;margin-top:8px;height:36px;border:0;border-radius:8px;background:#0f766e;color:white;font-weight:700;cursor:pointer;&quot;&gt;+ Add Insurance&lt;/button&gt;&#x27;
          +&#x27;&lt;/div&gt;&#x27;;
        document.body.appendChild(panel);

        // Minimize/expand the ALTA lead panel without affecting auto-save.
        const panelBody=document.getElementById(&#x27;tx-alta-body&#x27;);
        const panelHead=document.getElementById(&#x27;tx-alta-head&#x27;);
        const minimizeBtn=document.getElementById(&#x27;tx-alta-minimize&#x27;);
        function setPanelMinimized(minimized){
          if(panelBody) panelBody.style.display=minimized?&#x27;none&#x27;:&#x27;block&#x27;;
          if(panelHead) panelHead.style.marginBottom=minimized?&#x27;0&#x27;:&#x27;10px&#x27;;
          panel.style.width=minimized?&#x27;245px&#x27;:&#x27;315px&#x27;;
          if(minimizeBtn){
            minimizeBtn.textContent=minimized?&#x27;+&#x27;:&#x27;−&#x27;;
            minimizeBtn.title=minimized?&#x27;Expand&#x27;:&#x27;Minimize&#x27;;
            minimizeBtn.setAttribute(&#x27;aria-label&#x27;,(minimized?&#x27;Expand&#x27;:&#x27;Minimize&#x27;)+&#x27; TritoX Lead Info&#x27;);
          }
          GM_setValue(&#x27;tritox_alta_panel_minimized&#x27;,!!minimized);
        }
        setPanelMinimized(!!GM_getValue(&#x27;tritox_alta_panel_minimized&#x27;,false));
        if(minimizeBtn){
          minimizeBtn.addEventListener(&#x27;click&#x27;,function(e){
            e.preventDefault();
            e.stopPropagation();
            setPanelMinimized(panelBody &amp;&amp; panelBody.style.display!==&#x27;none&#x27;);
          });
        }

        const saveNow=function(){
          const now=altaIdentity();
          if(!now.id) return;
          const priorNow=priorInsuranceSnapshot();
          const prev=readSaved(now.id);
          const validInEffect=priorNow.visible ? priorNow.inEffect : !!prev.companyInEffect;
          const meta={
            name:now.name||state.name,
            altaId:now.id,
            company:validInEffect?document.getElementById(&#x27;tx-alta-company&#x27;).value.trim():&#x27;&#x27;,
            renewalDate:validInEffect?document.getElementById(&#x27;tx-alta-date&#x27;).value.trim():&#x27;&#x27;,
            star:document.getElementById(&#x27;tx-alta-star&#x27;).value,
            companyInEffect:validInEffect,
            pipDriverName:(driverSnapshot().selected||{}).name||prev.pipDriverName||&#x27;&#x27;,
            pipDriverDob:(driverSnapshot().selected||{}).dob||prev.pipDriverDob||&#x27;&#x27;,
            pipDriverAge:(driverSnapshot().selected||{}).age!=null?(driverSnapshot().selected||{}).age:(prev.pipDriverAge!=null?prev.pipDriverAge:null)
          };
          saveMeta(meta);
          const s=document.getElementById(&#x27;tx-alta-status&#x27;);
          if(s){s.textContent=&#x27;Saved ✓&#x27;;s.style.color=&#x27;#15803d&#x27;;setTimeout(function(){if(s){s.textContent=&#x27;Auto-saved&#x27;;s.style.color=&#x27;#64748b&#x27;;}},1200);}
        };
        document.getElementById(&#x27;tx-alta-save&#x27;).addEventListener(&#x27;click&#x27;,saveNow);
        const addInsuranceBtn=document.getElementById(&#x27;tx-alta-add-insurance&#x27;);
        if(addInsuranceBtn){
          addInsuranceBtn.addEventListener(&#x27;click&#x27;,function(e){
            e.preventDefault();
            e.stopPropagation();
            addDummyInsurance();
          });
        }
        document.getElementById(&#x27;tx-alta-company&#x27;).addEventListener(&#x27;change&#x27;,saveNow);
        document.getElementById(&#x27;tx-alta-date&#x27;).addEventListener(&#x27;change&#x27;,saveNow);
        document.getElementById(&#x27;tx-alta-star&#x27;).addEventListener(&#x27;change&#x27;,saveNow);
      }

      document.getElementById(&#x27;tx-alta-ident&#x27;).textContent=ident.name+&#x27;  •  ALTA #&#x27;+ident.id;
      const pip=driverSnapshot();
      const pipBox=document.getElementById(&#x27;tx-alta-pip-driver&#x27;);
      if(pipBox){
        if(pip.selected){
          pipBox.style.display=&#x27;block&#x27;;
          pipBox.style.borderColor=&#x27;#86efac&#x27;;
          pipBox.style.background=&#x27;#f0fdf4&#x27;;
          pipBox.style.color=&#x27;#166534&#x27;;
          pipBox.innerHTML=&#x27;&lt;strong&gt;PIP Driver (&amp;lt;65)&lt;/strong&gt;&lt;br&gt;&#x27;+pip.selected.name+&#x27;  •  &#x27;+pip.selected.dob+&#x27;  •  Age &#x27;+pip.selected.age;
        }else if(pip.visible){
          pipBox.style.display=&#x27;block&#x27;;
          pipBox.style.borderColor=&#x27;#cbd5e1&#x27;;
          pipBox.style.background=&#x27;#f8fafc&#x27;;
          pipBox.style.color=&#x27;#64748b&#x27;;
          const main=pip.main;
          if(main &amp;&amp; main.fullDob &amp;&amp; main.age&gt;=65){
            pipBox.innerHTML=&#x27;&lt;strong&gt;PIP Driver (&amp;lt;65)&lt;/strong&gt;&lt;br&gt;No accepted driver under 65 with a visible full DOB.&#x27;;
          }else{
            pipBox.innerHTML=&#x27;&lt;strong&gt;PIP Driver (&amp;lt;65)&lt;/strong&gt;&lt;br&gt;Waiting for an accepted driver DOB.&#x27;;
          }
        }else{
          pipBox.style.display=&#x27;none&#x27;;
        }
      }
      const comp=document.getElementById(&#x27;tx-alta-company&#x27;);
      const date=document.getElementById(&#x27;tx-alta-date&#x27;);
      const star=document.getElementById(&#x27;tx-alta-star&#x27;);
      if(comp &amp;&amp; !comp.value &amp;&amp; state.company) comp.value=state.company;
      if(date &amp;&amp; !date.value &amp;&amp; state.renewalDate) date.value=state.renewalDate;
      if(star){
        if(isBwCoveragePage()){
          // BW route is authoritative: force the panel to BW immediately.
          star.value=&#x27;BW&#x27;;
        }else if(onAutoCoverageSection()){
          // Auto-detect when ALTA displays the rating; otherwise leave blank so
          // the user can choose 1/2/3/BW manually.
          star.value=state.star||&#x27;&#x27;;
        }else if(!star.value &amp;&amp; state.star){
          star.value=state.star;
        }
      }

      // If ALTA exposes new values as the user moves to another quote page,
      // merge them without overwriting a manual choice already made.
      const current=readSaved(ident.id);
      const merged={
        name:ident.name, altaId:ident.id,
        company:state.companyInEffect?(current.company||state.company||&#x27;&#x27;):&#x27;&#x27;,
        renewalDate:state.companyInEffect?(current.renewalDate||state.renewalDate||&#x27;&#x27;):&#x27;&#x27;,
        star:isBwCoveragePage() ? &#x27;BW&#x27; : (onAutoCoverageSection() ? (state.star||&#x27;&#x27;) : (current.star||state.star||&#x27;&#x27;)),
        companyInEffect:!!state.companyInEffect,
        pipDriverName:(pip.selected&amp;&amp;pip.selected.name)||current.pipDriverName||&#x27;&#x27;,
        pipDriverDob:(pip.selected&amp;&amp;pip.selected.dob)||current.pipDriverDob||&#x27;&#x27;,
        pipDriverAge:(pip.selected&amp;&amp;pip.selected.age!=null)?pip.selected.age:(current.pipDriverAge!=null?current.pipDriverAge:null)
      };
      if(merged.company || merged.renewalDate || merged.star || prior.visible) saveMeta(merged);
    }

    buildPanel();
    applyAltaCoverageDefaults();
    // ALTA is a SPA. Re-read the page as the quote moves between sections.
    let lastSig=&#x27;&#x27;;
    setInterval(function(){
      const i=altaIdentity();
      const ds=driverSnapshot(); const sd=ds.selected;
      const sig=i.id+&#x27;|&#x27;+location.pathname+&#x27;|&#x27;+detectCarrier()+&#x27;|&#x27;+detectRenewalDate()+&#x27;|&#x27;+detectStar()+&#x27;|&#x27;+(sd?(sd.name+&#x27;|&#x27;+sd.dob+&#x27;|&#x27;+sd.age):&#x27;no-pip-driver&#x27;);
      if(sig!==lastSig){
        lastSig=sig;
        coveragePresetDoneSig=&#x27;&#x27;;
        buildPanel();
      }
      applyAltaCoverageDefaults();
    },500);
    return;
  }
  if(window.location.hostname === &#x27;tritoxtech.github.io&#x27; || (window.location.hostname === &#x27;saravanatritox-cloud.github.io&#x27; &amp;&amp; window.location.pathname.startsWith(&#x27;/aaron/&#x27;))){
    console.log(&#x27;[TritoX TM] Running on TritoX page&#x27;);

    // Aaron&#x27;s current GitHub page omits the processed PDF filename from
    // buildAZData(). Patch it at runtime so AgencyZoom can retrieve the exact
    // cached PDF that belongs to the selected quote.
    (function patchAaronFilenameTransfer(){
      const pageWindow=typeof unsafeWindow!==&#x27;undefined&#x27;?unsafeWindow:window;
      let attempts=0;
      const timer=setInterval(function(){
        attempts++;
        const original=pageWindow.buildAZData;
        if(typeof original===&#x27;function&#x27; &amp;&amp; !original.__tritoxFilenamePatched){
          const patched=function(result){
            const data=original.apply(this,arguments);
            if(data &amp;&amp; result){
              if(result.filename) data._filename=result.filename;
              // Transfer QC routing flags so AgencyZoom can choose the correct tags.
              data._highPrice=!!result.putInStop;
              if(result.homeData) data._isBundle=!!result.homeData.isBundle;
            }
            return data;
          };
          patched.__tritoxFilenamePatched=true;
          pageWindow.buildAZData=patched;
          clearInterval(timer);
          console.log(&#x27;[TritoX TM] Aaron filename transfer patch installed&#x27;);
        }else if(original &amp;&amp; original.__tritoxFilenamePatched){
          clearInterval(timer);
        }else if(attempts&gt;=80){
          clearInterval(timer);
          console.warn(&#x27;[TritoX TM] Aaron buildAZData was not found&#x27;);
        }
      },250);
    })();

    function pdfStorageKey(name){
      let h=2166136261;
      const s=String(name||&#x27;quote.pdf&#x27;).toLowerCase();
      for(let i=0;i&lt;s.length;i++){
        h^=s.charCodeAt(i);
        h=Math.imul(h,16777619);
      }
      return &#x27;tritox_pdf_&#x27;+(h&gt;&gt;&gt;0).toString(16);
    }

    function cachePdf(file){
      if(!file || !/.pdf$/i.test(file.name)) return;
      if(file.size&gt;20*1024*1024){
        console.warn(&#x27;[TritoX TM] PDF is larger than 20 MB and was not cached:&#x27;,file.name);
        return;
      }
      const reader=new FileReader();
      reader.onload=function(){
        try{
          GM_setValue(pdfStorageKey(file.name),JSON.stringify({
            name:file.name,
            type:file.type||&#x27;application/pdf&#x27;,
            size:file.size,
            lastModified:file.lastModified||Date.now(),
            dataUrl:String(reader.result),
            savedAt:Date.now()
          }));
          console.log(&#x27;[TritoX TM] PDF cached for AgencyZoom:&#x27;,file.name);
        }catch(err){
          console.error(&#x27;[TritoX TM] Could not cache PDF:&#x27;,err);
        }
      };
      reader.readAsDataURL(file);
    }

    // Capture PDFs selected or dropped into Aaron QC. Each file is stored under
    // its filename so bulk processing can still match the correct customer PDF.
    document.addEventListener(&#x27;change&#x27;,function(e){
      if(e.target &amp;&amp; e.target.id===&#x27;fileInput&#x27; &amp;&amp; e.target.files){
        Array.from(e.target.files).forEach(cachePdf);
      }
    },true);
    document.addEventListener(&#x27;drop&#x27;,function(e){
      if(e.dataTransfer &amp;&amp; e.dataTransfer.files &amp;&amp; e.target.closest &amp;&amp; e.target.closest(&#x27;#uploadZone&#x27;)){
        Array.from(e.dataTransfer.files).forEach(cachePdf);
      }
    },true);

    function mirrorCurrentAgencyZoomLead(){
      try{
        const raw=GM_getValue(&#x27;tritox_current_az_lead&#x27;,&#x27;&#x27;);
        if(raw) localStorage.setItem(&#x27;tritox_current_az_lead&#x27;,String(raw));
      }catch(e){}
    }
    mirrorCurrentAgencyZoomLead();
    setInterval(mirrorCurrentAgencyZoomLead,200);

    function checkTritoXData(){
      try{
        const raw = localStorage.getItem(&#x27;tritox_az_data&#x27;);
        if(!raw) return;
        const data = JSON.parse(raw);
        if(!data || !data._name || !data._ts) return;

        // Find the correct ALTA metadata from the multi-lead cache.
        // This supports processing 4-6 ALTA quotes first, then uploading PDFs
        // later in any order without reopening or re-saving each lead.
        try{
          const norm=function(v){
            return String(v||&#x27;&#x27;).toLowerCase().replace(/[^a-z0-9]+/g,&#x27; &#x27;).trim().replace(/\s+/g,&#x27; &#x27;);
          };
          const firstLast=function(v){
            const p=norm(v).split(&#x27; &#x27;).filter(Boolean);
            return p.length&gt;=2 ? p[0]+&#x27; &#x27;+p[p.length-1] : p.join(&#x27; &#x27;);
          };
          const indexRaw=GM_getValue(&#x27;tritox_alta_index&#x27;,&#x27;{}&#x27;);
          const index=JSON.parse(indexRaw||&#x27;{}&#x27;)||{};
          const now=Date.now();
          const valid=Object.keys(index).map(function(k){return index[k];}).filter(function(a){
            return a &amp;&amp; a.name &amp;&amp; a._savedAt &amp;&amp; now-Number(a._savedAt)&lt;=7200000;
          });

          let a=valid.find(function(x){return norm(x.name)===norm(data._name);})||null;

          // If the PDF includes/omits a middle name or initial, use first+last
          // only when exactly one cached lead matches that pair.
          if(!a){
            const key=firstLast(data._name);
            const matches=valid.filter(function(x){return firstLast(x.name)===key;});
            if(matches.length===1) a=matches[0];
          }

          // If QC had to fall back to a surname-only filename
          // (e.g. Galloway-townsend_Auto_09282026.pdf), recover the FULL ALTA
          // customer name only when exactly one cached lead has that surname.
          if(!a){
            const parts=norm(data._name).split(&#x27; &#x27;).filter(Boolean);
            const filenameBase=String(data._filename||&#x27;&#x27;)
              .replace(/\.pdf$/i,&#x27;&#x27;)
              .replace(/[_\-\s]+(?:auto|bundle|home)[_\-\s]+\d{8}$/i,&#x27;&#x27;)
              .replace(/_/g,&#x27; &#x27;)
              .trim();
            const shortName=norm(filenameBase||data._name);
            const shortCompact=shortName.replace(/\s+/g,&#x27;&#x27;);
            const surnameMatches=valid.filter(function(x){
              const xp=norm(x.name).split(&#x27; &#x27;).filter(Boolean);
              if(!xp.length) return false;
              const last=xp[xp.length-1];
              const compound=xp.length&gt;=2 ? xp[xp.length-2]+xp[xp.length-1] : last;
              return shortCompact===last.replace(/\s+/g,&#x27;&#x27;) ||
                     shortCompact===compound.replace(/\s+/g,&#x27;&#x27;);
            });
            if(surnameMatches.length===1){
              a=surnameMatches[0];
              data._name=a.name; // restore full first + last name for AZ button/guard
              localStorage.setItem(&#x27;tritox_az_data&#x27;,JSON.stringify(data));
              console.log(&#x27;[TritoX TM] Recovered full name from unique ALTA surname:&#x27;,
                filenameBase,&#x27;=&gt;&#x27;,a.name);
            }
          }

          // Backward-compatible fallback for data captured before v4.36.
          if(!a){
            const latestRaw=GM_getValue(&#x27;tritox_alta_latest&#x27;,&#x27;&#x27;);
            if(latestRaw){
              const latest=JSON.parse(latestRaw);
              if(latest &amp;&amp; latest.name &amp;&amp; norm(latest.name)===norm(data._name)) a=latest;
            }
          }

          if(a){
            data.current_company=a.companyInEffect ? (a.company||&#x27;&#x27;) : &#x27;&#x27;;
            data.auto_renewal_date=a.companyInEffect ? (a.renewalDate||&#x27;&#x27;) : &#x27;&#x27;;
            data.star_rating=a.star||&#x27;&#x27;;
            data._altaId=a.altaId||&#x27;&#x27;;
            data._altaSavedAt=a._savedAt||0;
            localStorage.setItem(&#x27;tritox_az_data&#x27;,JSON.stringify(data));
            console.log(&#x27;[TritoX TM] Multi-lead ALTA match:&#x27;,data._name,&#x27;=&gt;&#x27;,a.name,a.altaId);
          }else{
            // Explicitly keep these blank rather than borrowing another lead&#x27;s data.
            data.current_company=&#x27;&#x27;;
            data.auto_renewal_date=&#x27;&#x27;;
            data.star_rating=&#x27;&#x27;;
            data._altaId=&#x27;&#x27;;
            data._altaSavedAt=0;
            localStorage.setItem(&#x27;tritox_az_data&#x27;,JSON.stringify(data));
            console.log(&#x27;[TritoX TM] No ALTA cache match for:&#x27;,data._name);
          }
        }catch(e){ console.warn(&#x27;[TritoX TM] ALTA multi-lead merge skipped:&#x27;,e); }

        const gmRaw = GM_getValue(&#x27;tritox_az_data&#x27;,&#x27;&#x27;);
        let gmData={};
        try{ gmData = JSON.parse(gmRaw||&#x27;{}&#x27;)||{}; }catch(e){}

        // Keep the AgencyZoom lead-ID binding ONLY for the exact same processed
        // PDF/QC result. Never copy a previous customer&#x27;s bound ID to a new PDF.
        const sameBoundResult=
          Number(gmData._ts||0)===Number(data._ts||0) &amp;&amp;
          String(gmData._filename||&#x27;&#x27;)===String(data._filename||&#x27;&#x27;);
        if(sameBoundResult &amp;&amp; gmData._leadId &amp;&amp; !data._leadId){
          data._leadId=String(gmData._leadId);
          data._leadNameBound=String(gmData._leadNameBound||&#x27;&#x27;);
        }

        const gmTs = Number(gmData._ts||0);
        const metaChanged =
          String(gmData.current_company||&#x27;&#x27;)!==String(data.current_company||&#x27;&#x27;) ||
          String(gmData.auto_renewal_date||&#x27;&#x27;)!==String(data.auto_renewal_date||&#x27;&#x27;) ||
          String(gmData.star_rating||&#x27;&#x27;)!==String(data.star_rating||&#x27;&#x27;) ||
          String(gmData._altaId||&#x27;&#x27;)!==String(data._altaId||&#x27;&#x27;);
        if(data._ts &gt; gmTs || (data._ts===gmTs &amp;&amp; metaChanged)){
          GM_setValue(&#x27;tritox_az_data&#x27;, JSON.stringify(data));
          console.log(&#x27;[TritoX TM] Saved to GM for:&#x27;, data._name, &#x27;metadata changed:&#x27;,metaChanged);
        }
      }catch(e){ console.log(&#x27;[TritoX TM] Error:&#x27;, e); }
    }
    setInterval(checkTritoXData, 1000);
    return;
  }

  console.log(&#x27;[TritoX TM] Running on AgencyZoom page&#x27;);

  function normalizeLeadName(value){
    return String(value||&#x27;&#x27;).toLowerCase().replace(/[^a-z0-9]+/g,&#x27; &#x27;).trim().replace(/\s+/g,&#x27; &#x27;);
  }

  // v4.44: Merge ALTA metadata again at the exact moment Fill is clicked.
  // This removes timing dependence on the QC bridge and is important when
  // several ALTA leads are quoted first, then PDFs are processed later.
  function mergeAltaMetadataAtFill(data){
    try{
      if(!data || !data._name) return data;
      const indexRaw=GM_getValue(&#x27;tritox_alta_index&#x27;,&#x27;{}&#x27;);
      const index=JSON.parse(indexRaw||&#x27;{}&#x27;)||{};
      const now=Date.now();
      const valid=Object.keys(index).map(function(k){return index[k];}).filter(function(a){
        return a &amp;&amp; a.name &amp;&amp; a._savedAt &amp;&amp; now-Number(a._savedAt)&lt;=7200000;
      });
      const target=normalizeLeadName(data._name);
      let matches=valid.filter(function(a){return normalizeLeadName(a.name)===target;});

      if(!matches.length){
        const parts=target.split(&#x27; &#x27;).filter(Boolean);
        const key=parts.length&gt;=2 ? parts[0]+&#x27; &#x27;+parts[parts.length-1] : target;
        const loose=valid.filter(function(a){
          const p=normalizeLeadName(a.name).split(&#x27; &#x27;).filter(Boolean);
          const k=p.length&gt;=2 ? p[0]+&#x27; &#x27;+p[p.length-1] : p.join(&#x27; &#x27;);
          return k===key;
        });
        if(loose.length===1) matches=loose;
      }

      // Surname-only recovery for filename fallbacks. Use only when unique.
      if(!matches.length){
        const base=String(data._filename||&#x27;&#x27;)
          .replace(/\.pdf$/i,&#x27;&#x27;)
          .replace(/[_\-\s]+(?:auto|bundle|home)[_\-\s]+\d{8}$/i,&#x27;&#x27;)
          .replace(/_/g,&#x27; &#x27;)
          .trim();
        const compact=normalizeLeadName(base||data._name).replace(/\s+/g,&#x27;&#x27;);
        const bySurname=valid.filter(function(a){
          const p=normalizeLeadName(a.name).split(&#x27; &#x27;).filter(Boolean);
          if(!p.length) return false;
          const last=p[p.length-1];
          const compound=p.length&gt;=2 ? p[p.length-2]+p[p.length-1] : last;
          return compact===last.replace(/\s+/g,&#x27;&#x27;) ||
                 compact===compound.replace(/\s+/g,&#x27;&#x27;);
        });
        if(bySurname.length===1){
          matches=bySurname;
          data._name=bySurname[0].name;
          console.log(&#x27;[TritoX TM] Direct full-name recovery:&#x27;,base,&#x27;=&gt;&#x27;,data._name);
        }
      }

      if(matches.length){
        matches.sort(function(a,b){return Number(b._savedAt||0)-Number(a._savedAt||0);});
        const a=matches[0];
        data.current_company=a.companyInEffect ? String(a.company||&#x27;&#x27;) : &#x27;&#x27;;
        data.auto_renewal_date=a.companyInEffect ? String(a.renewalDate||&#x27;&#x27;) : &#x27;&#x27;;
        data.star_rating=String(a.star||&#x27;&#x27;);
        data._altaId=String(a.altaId||&#x27;&#x27;);
        data._altaSavedAt=Number(a._savedAt||0);
        console.log(&#x27;[TritoX TM] Direct ALTA merge at Fill:&#x27;,data._name,data.current_company,data.auto_renewal_date,data.star_rating,data._altaId);
      }else{
        console.log(&#x27;[TritoX TM] No direct ALTA match at Fill for:&#x27;,data._name);
      }
      return data;
    }catch(e){
      console.warn(&#x27;[TritoX TM] Direct ALTA merge failed:&#x27;,e);
      return data;
    }
  }

  function getVisibleLeadHeaderText(){
    const pieces=[];
    const selectors=[
      &#x27;#referral-container&#x27;,
      &#x27;[class*=\&quot;lead-header\&quot;]&#x27;,&#x27;[class*=\&quot;referral-header\&quot;]&#x27;,&#x27;[class*=\&quot;contact-header\&quot;]&#x27;,
      &#x27;[class*=\&quot;leadHeader\&quot;]&#x27;,&#x27;[class*=\&quot;referralHeader\&quot;]&#x27;,&#x27;[class*=\&quot;contactHeader\&quot;]&#x27;
    ];
    selectors.forEach(function(selector){
      document.querySelectorAll(selector).forEach(function(el){
        const r=el.getBoundingClientRect();
        if(r.width&gt;0 &amp;&amp; r.height&gt;0 &amp;&amp; r.top&lt;220){
          pieces.push(el.innerText||el.textContent||&#x27;&#x27;);
        }
      });
    });
    // AgencyZoom&#x27;s lead name is often outside #referral-container. Capture only
    // visible text in the upper lead pane so old activity/history names do not count.
    document.querySelectorAll(&#x27;h1,h2,h3,h4,strong,b,span,div&#x27;).forEach(function(el){
      const r=el.getBoundingClientRect();
      if(r.width&lt;=0 || r.height&lt;=0 || r.top&lt;0 || r.top&gt;150 || r.left&lt;0) return;
      const text=(el.textContent||&#x27;&#x27;).trim();
      if(text &amp;&amp; text.length&lt;=100) pieces.push(text);
    });
    return normalizeLeadName(pieces.join(&#x27; | &#x27;));
  }

  function currentLeadMatchesData(data){
    const wanted=normalizeLeadName(data &amp;&amp; data._name);
    if(!wanted) return true;
    const header=getVisibleLeadHeaderText();
    return !!header &amp;&amp; header.includes(wanted);
  }

  // Fill popup lifecycle: same 7-second untouched window used by the
  // original Aaron/TritoX workflow. Once a popup expires/cancels, the same
  // processed lead will not be recreated; any newer lead replaces it instantly.
  let expiredTs = Number(GM_getValue(&#x27;tritox_popup_expired_ts&#x27;,0)) || 0;

  function expireFillPopup(ts){
    expiredTs=Math.max(expiredTs,Number(ts||0));
    GM_setValue(&#x27;tritox_popup_expired_ts&#x27;,expiredTs);
    const oldBtn=document.getElementById(&#x27;tritox-fill-btn&#x27;);
    if(oldBtn) oldBtn.remove();
    const oldOverlay=document.getElementById(&#x27;tritox-fill-overlay&#x27;);
    if(oldOverlay) oldOverlay.remove();
  }

  function addFillButton(){
    const raw = GM_getValue(&#x27;tritox_az_data&#x27;,&#x27;&#x27;);
    if(!raw) return;
    let data;
    try{ data = JSON.parse(raw); }catch(e){ return; }
    if(!data || !data._name) return;

    // Bind the processed PDF/QC result to this AgencyZoom lead ID as soon as a
    // safe customer-name match is available. Future checks use the ID first.
    data=bindDataToCurrentLeadId(data);

    const buttonDataTs = data._ts || 0;

    // Do not recreate data that was already filled.
    if(buttonDataTs &lt;= filledTs || buttonDataTs &lt;= expiredTs) return;

    const age = Date.now() - buttonDataTs;
    if(age &gt; 7200000) return;

    // If a new PDF/lead arrives while AgencyZoom stays on the same URL,
    // replace the previous Fill button immediately.
    const existingBtn=document.getElementById(&#x27;tritox-fill-btn&#x27;);
    if(existingBtn){
      const existingTs=Number(existingBtn.dataset.tritoxTs||0);
      if(existingTs===buttonDataTs) return;
      existingBtn.remove();
    }

    const btn = document.createElement(&#x27;div&#x27;);
    btn.dataset.tritoxTs=String(buttonDataTs);
    btn.id = &#x27;tritox-fill-btn&#x27;;
    btn.style.cssText = &#x27;position:fixed;top:80px;right:20px;z-index:99999;background:linear-gradient(135deg,#00d4ff,#7b2fff);color:#fff;padding:10px 16px;border-radius:10px;cursor:pointer;font-size:13px;font-weight:700;box-shadow:0 4px 20px rgba(0,212,255,0.4);font-family:sans-serif;text-align:center;min-width:160px;&#x27;;
    btn.innerHTML = &#x27;🚀 Fill + Attach PDF&lt;br&gt;&lt;span style=&quot;font-size:11px;font-weight:400;opacity:0.9;&quot;&gt;&#x27; + data._name + &#x27;&lt;/span&gt;&#x27;;

    // Initial Fill button is visible for a maximum of 7 seconds if untouched.
    let autoHideTimer=null;
    let manualMismatchOverride=false;

    btn.addEventListener(&#x27;click&#x27;, function(){
      if(autoHideTimer){ clearTimeout(autoHideTimer); autoHideTimer=null; }

      // v4.45.52 PDF / AgencyZoom lead guard.
      // Never fill fields, attach a PDF, add tags, or click Update when the
      // customer in the processed PDF does not match the open AgencyZoom lead.
      const identityCheck=validateLeadIdentity(data);
      if(identityCheck.mismatch &amp;&amp; !manualMismatchOverride){
        const reasons=[];
        if(identityCheck.idMatch===false){
          reasons.push(&#x27;Bound lead ID: &#x27;+identityCheck.expectedId+&#x27; | Open lead ID: &#x27;+identityCheck.leadId);
        }else if(identityCheck.idMatch===null &amp;&amp; identityCheck.nameMatch===false){
          reasons.push(&#x27;PDF customer: &#x27;+(identityCheck.pdfName||&#x27;Unknown&#x27;)+&#x27; | AgencyZoom lead: &#x27;+(identityCheck.leadName||&#x27;Unknown&#x27;));
        }

        const proceed=window.confirm(
          &#x27;⚠️ WRONG LEAD / PDF WARNING\n\n&#x27;
          +reasons.join(&#x27;\n&#x27;)
          +&#x27;\n\nPlease check the lead manually.\n\n&#x27;
          +&#x27;OK = Proceed Anyway\nCancel = Stop&#x27;
        );

        if(!proceed) return;

        manualMismatchOverride=true;
        console.warn(&#x27;[TritoX TM] Manual mismatch override approved for:&#x27;,
          data._name,&#x27;open lead:&#x27;,identityCheck.leadName,identityCheck.leadId);
      }

      // Show confirmation popup
      const oldOverlay=document.getElementById(&#x27;tritox-fill-overlay&#x27;);
      if(oldOverlay) oldOverlay.remove();
      const overlay = document.createElement(&#x27;div&#x27;);
      overlay.id=&#x27;tritox-fill-overlay&#x27;;
      overlay.dataset.tritoxTs=String(buttonDataTs);
      overlay.style.cssText = &#x27;position:fixed;inset:0;background:rgba(0,0,0,0.7);z-index:999999;display:flex;align-items:center;justify-content:center;&#x27;;
      const box = document.createElement(&#x27;div&#x27;);
      box.style.cssText = &#x27;background:#fff;border-radius:16px;padding:28px 32px;max-width:380px;width:90%;text-align:center;font-family:sans-serif;box-shadow:0 20px 60px rgba(0,0,0,0.3);&#x27;;
      box.innerHTML = &#x27;&lt;div style=&quot;font-size:32px;margin-bottom:12px;&quot;&gt;⚠️&lt;/div&gt;&#x27;
        +&#x27;&lt;div style=&quot;font-size:16px;font-weight:700;color:#1a1a2e;margin-bottom:8px;&quot;&gt;Confirm Fill + PDF Attachment&lt;/div&gt;&#x27;
        +&#x27;&lt;div style=&quot;font-size:13px;color:#666;margin-bottom:6px;&quot;&gt;You are about to fill and attach the quote PDF for:&lt;/div&gt;&#x27;
        +&#x27;&lt;div style=&quot;font-size:15px;font-weight:700;color:#7b2fff;margin-bottom:10px;padding:10px;background:#f0e8ff;border-radius:8px;&quot;&gt;&#x27;+data._name+&#x27;&lt;/div&gt;&#x27;
        +(manualMismatchOverride
          ? &#x27;&lt;div style=&quot;font-size:12px;color:#b45309;margin-bottom:8px;font-weight:700;padding:8px;background:#fff7ed;border:1px solid #fdba74;border-radius:7px;&quot;&gt;⚠ Manual override — verify this is the correct AgencyZoom lead before filling.&lt;/div&gt;&#x27;
          : (identityCheck.idMatch===true
            ? &#x27;&lt;div style=&quot;font-size:12px;color:#15803d;margin-bottom:6px;font-weight:700;&quot;&gt;✅ Lead ID matched: &#x27;+identityCheck.leadId+&#x27;&lt;/div&gt;&#x27;
            : (identityCheck.nameMatch===true
              ? &#x27;&lt;div style=&quot;font-size:12px;color:#15803d;margin-bottom:6px;font-weight:700;&quot;&gt;✅ Customer matched — binding Lead ID: &#x27;+(identityCheck.leadId||&#x27;Not detected&#x27;)+&#x27;&lt;/div&gt;&#x27;
              : &#x27;&lt;div style=&quot;font-size:12px;color:#b45309;margin-bottom:6px;font-weight:700;&quot;&gt;⚠ AgencyZoom customer/ID could not be verified automatically.&lt;/div&gt;&#x27;)))
        +&#x27;&lt;div style=&quot;font-size:12px;color:#666;margin-bottom:20px;&quot;&gt;AgencyZoom Lead ID: &#x27;+(identityCheck.leadId||&#x27;Not detected&#x27;)+(identityCheck.expectedId?&#x27; | Bound ID: &#x27;+identityCheck.expectedId:&#x27;&#x27;)+&#x27;&lt;/div&gt;&#x27;
        +&#x27;&lt;div style=&quot;display:flex;gap:10px;justify-content:center;&quot;&gt;&#x27;
        +&#x27;&lt;button id=&quot;tritox-cancel&quot; style=&quot;flex:1;padding:10px;border:2px solid #ddd;background:#fff;border-radius:8px;cursor:pointer;font-size:13px;font-weight:600;color:#666;&quot;&gt;❌ Cancel&lt;/button&gt;&#x27;
        +&#x27;&lt;button id=&quot;tritox-confirm&quot; style=&quot;flex:1;padding:10px;border:none;background:linear-gradient(135deg,#00d4ff,#7b2fff);border-radius:8px;cursor:pointer;font-size:13px;font-weight:700;color:#fff;&quot;&gt;✅ Fill + Attach&lt;/button&gt;&#x27;
        +&#x27;&lt;/div&gt;&#x27;;
      overlay.appendChild(box);
      document.body.appendChild(overlay);

      // Cancel
      document.getElementById(&#x27;tritox-cancel&#x27;).addEventListener(&#x27;click&#x27;, function(){
        overlay.remove();
        expireFillPopup(buttonDataTs);
      });

      // Confirm
      document.getElementById(&#x27;tritox-confirm&#x27;).addEventListener(&#x27;click&#x27;, async function(){
        const finalIdentity=validateLeadIdentity(data);
        if(finalIdentity.mismatch &amp;&amp; !manualMismatchOverride){
          const reasons=[];
          if(finalIdentity.idMatch===false){
            reasons.push(&#x27;Bound lead ID: &#x27;+finalIdentity.expectedId+&#x27; | Open lead ID: &#x27;+finalIdentity.leadId);
          }else if(finalIdentity.idMatch===null &amp;&amp; finalIdentity.nameMatch===false){
            reasons.push(&#x27;PDF customer: &#x27;+(finalIdentity.pdfName||&#x27;Unknown&#x27;)+&#x27; | AgencyZoom lead: &#x27;+(finalIdentity.leadName||&#x27;Unknown&#x27;));
          }

          const proceedFinal=window.confirm(
            &#x27;⚠️ LEAD CHANGED / PDF MISMATCH\n\n&#x27;
            +reasons.join(&#x27;\n&#x27;)
            +&#x27;\n\nPlease verify manually.\n\n&#x27;
            +&#x27;OK = Proceed Anyway\nCancel = Stop&#x27;
          );

          if(!proceedFinal) return;
          manualMismatchOverride=true;
        }

        const tritoxStart=performance.now();
        overlay.remove();
        btn.style.background=&#x27;linear-gradient(135deg,#0085ff,#7b2fff)&#x27;;
        btn.innerHTML=&#x27;⏳ Attaching PDF…&lt;br&gt;&lt;span style=&quot;font-size:11px;font-weight:400;opacity:0.9;&quot;&gt;&#x27;+data._name+&#x27;&lt;/span&gt;&#x27;;

        // v4.22 ULTRAFAST: keep the lead on Main, fill immediately, and run
        // PDF upload + tag save in parallel. This removes the slow Files-tab
        // verification/repaint cycle that previously consumed most of the time.
        if(!document.getElementById(&#x27;customfields-cf30203&#x27;)){
          await openLeadTab(&#x27;Main&#x27;);
          await waitFor(function(){return document.getElementById(&#x27;customfields-cf30203&#x27;);},800);
        }
        // Always refresh the correct ALTA record immediately before filling.
        // This makes company/date/Star-BW work even when QC was processed before
        // the latest ALTA auto-save or when several leads are handled in a batch.
        mergeAltaMetadataAtFill(data);
        fillFields(data);

        // Start PDF upload immediately. Give AgencyZoom a very short moment to
        // commit the Main-page custom-field/select changes before opening Add Tag.
        // This prevents the Star field/selectpicker update from racing the tag modal.
        const pdfPromise=attachPdfFast(data);
        await wait(180);
        let tagResult=await applyQuoteTags(data);
        if(!tagResult.ok){
          // One fast retry handles transient AgencyZoom re-renders without
          // changing the user&#x27;s working field/PDF flow.
          await wait(140);
          tagResult=await applyQuoteTags(data);
        }
        const attachResult=await pdfPromise;

        // Separate-script change: after all existing field/PDF/tag work is done,
        // automatically save the AgencyZoom Main form by clicking Update.
        await wait(120);
        const updateResult=await clickAgencyZoomUpdate();

        console.log(&#x27;[TritoX TM] Total automation time:&#x27;, Math.round(performance.now()-tritoxStart)+&#x27;ms&#x27;, {pdf:attachResult, tags:tagResult, update:updateResult});
        // Store timestamp of this fill to prevent reappearing
        try{
          const d = JSON.parse(GM_getValue(&#x27;tritox_az_data&#x27;,&#x27;{}&#x27;));
          filledTs = d._ts || Date.now();
        }catch(e){ filledTs = Date.now(); }
        GM_setValue(&#x27;tritox_az_data&#x27;,&#x27;&#x27;);
        if(attachResult.ok &amp;&amp; data._filename){
          GM_deleteValue(pdfStorageKey(data._filename));
        }

        // Show done state with close button and countdown
        let secs = 1;
        btn.style.background = attachResult.ok &amp;&amp; tagResult.ok ? &#x27;linear-gradient(135deg,#00e887,#00b359)&#x27; : &#x27;#a65b00&#x27;;
        btn.style.minWidth = &#x27;180px&#x27;;

        function updateBtn(){
          btn.innerHTML = (attachResult.ok &amp;&amp; tagResult.ok?&#x27;✅ Filled + Verified PDF + Tags&#x27;:&#x27;⚠️ Filled — Review PDF / Tags&#x27;)+&#x27;&lt;br&gt;&#x27;
            +&#x27;&lt;span style=&quot;font-size:11px;font-weight:400;opacity:0.9;&quot;&gt;&#x27; + data._name + &#x27;&lt;/span&gt;&lt;br&gt;&#x27;
            +&#x27;&lt;span style=&quot;font-size:10px;font-weight:400;opacity:0.9;&quot;&gt;&#x27;+attachResult.message+&#x27;&lt;/span&gt;&lt;br&gt;&#x27;
            +&#x27;&lt;span style=&quot;font-size:10px;font-weight:400;opacity:0.9;&quot;&gt;&#x27;+tagResult.message+&#x27;&lt;/span&gt;&lt;br&gt;&#x27;
            +&#x27;&lt;span style=&quot;font-size:10px;font-weight:400;opacity:0.9;&quot;&gt;&#x27;+updateResult.message+&#x27;&lt;/span&gt;&lt;br&gt;&#x27;
            +&#x27;&lt;div style=&quot;display:flex;align-items:center;justify-content:center;gap:8px;margin-top:6px;&quot;&gt;&#x27;
            +&#x27;&lt;span style=&quot;font-size:10px;opacity:0.8;&quot;&gt;Auto close in &#x27;+secs+&#x27;s&lt;/span&gt;&#x27;
            +&#x27;&lt;button id=&quot;tritox-close-btn&quot; style=&quot;background:rgba(255,255,255,0.25);border:1px solid rgba(255,255,255,0.5);color:#fff;border-radius:6px;padding:2px 8px;cursor:pointer;font-size:11px;font-weight:700;&quot;&gt;✕ Close&lt;/button&gt;&#x27;
            +&#x27;&lt;/div&gt;&#x27;;

          // Attach close button listener after innerHTML update
          const closeBtn = document.getElementById(&#x27;tritox-close-btn&#x27;);
          if(closeBtn){
            closeBtn.addEventListener(&#x27;click&#x27;, function(e){
              e.stopPropagation();
              btn.remove();
            });
          }
        }

        updateBtn();

        // Countdown timer
        const timer = setInterval(function(){
          secs--;
          if(secs &lt;= 0){
            clearInterval(timer);
            btn.remove();
          } else {
            updateBtn();
          }
        }, 400);
      });

      // Click outside to cancel
      overlay.addEventListener(&#x27;click&#x27;, function(e){
        if(e.target === overlay){
          overlay.remove();
          expireFillPopup(buttonDataTs);
        }
      });
    });

    document.body.appendChild(btn);

    // If untouched, disappear after 7 seconds and do not recreate this same
    // lead. Processing a new PDF creates a newer timestamp and shows immediately.
    autoHideTimer=setTimeout(function(){
      if(btn.isConnected) btn.remove();
      expiredTs=Math.max(expiredTs,buttonDataTs);
      GM_setValue(&#x27;tritox_popup_expired_ts&#x27;,expiredTs);
    },7000);

  }

  function pdfStorageKey(name){
    let h=2166136261;
    const s=String(name||&#x27;quote.pdf&#x27;).toLowerCase();
    for(let i=0;i&lt;s.length;i++){
      h^=s.charCodeAt(i);
      h=Math.imul(h,16777619);
    }
    return &#x27;tritox_pdf_&#x27;+(h&gt;&gt;&gt;0).toString(16);
  }

  function wait(ms){return new Promise(function(resolve){setTimeout(resolve,ms);});}

  async function clickAgencyZoomUpdate(){
    function findUpdateButton(){
      const selectors=[
        &#x27;#referral-container button.btn.btn-primary.action[onclick*=&quot;leadDetailTab.doSave&quot;]&#x27;,
        &#x27;#referral-container button[onclick*=&quot;leadDetailTab.doSave&quot;]&#x27;,
        &#x27;button.btn.btn-primary.action[onclick*=&quot;leadDetailTab.doSave&quot;]&#x27;,
        &#x27;button[onclick*=&quot;leadDetailTab.doSave&quot;]&#x27;
      ];
      for(const selector of selectors){
        const buttons=Array.from(document.querySelectorAll(selector));
        const found=buttons.find(function(btn){
          try{
            const r=btn.getBoundingClientRect();
            const visible=r.width&gt;0 &amp;&amp; r.height&gt;0;
            const text=String(btn.textContent||btn.value||&#x27;&#x27;).trim().toLowerCase();
            return visible &amp;&amp; text===&#x27;update&#x27; &amp;&amp; !btn.disabled &amp;&amp; btn.getAttribute(&#x27;aria-disabled&#x27;)!==&#x27;true&#x27;;
          }catch(e){ return false; }
        });
        if(found) return found;
      }

      const roots=[
        document.getElementById(&#x27;referral-container&#x27;),
        document.querySelector(&#x27;#detailDockform&#x27;),
        document
      ].filter(Boolean);
      for(const root of roots){
        const found=Array.from(root.querySelectorAll(&#x27;button,input[type=&quot;button&quot;],input[type=&quot;submit&quot;]&#x27;)).find(function(btn){
          try{
            const r=btn.getBoundingClientRect();
            const visible=r.width&gt;0 &amp;&amp; r.height&gt;0;
            const text=String(btn.textContent||btn.value||&#x27;&#x27;).trim().toLowerCase();
            return visible &amp;&amp; text===&#x27;update&#x27; &amp;&amp; !btn.disabled &amp;&amp; btn.getAttribute(&#x27;aria-disabled&#x27;)!==&#x27;true&#x27;;
          }catch(e){ return false; }
        });
        if(found) return found;
      }
      return null;
    }

    const start=Date.now();
    let btn=null;
    while(Date.now()-start&lt;2500){
      btn=findUpdateButton();
      if(btn) break;
      await wait(80);
    }
    if(!btn) return {ok:false,message:&#x27;Update button was not found&#x27;};

    try{ btn.scrollIntoView({block:&#x27;center&#x27;,inline:&#x27;nearest&#x27;}); }catch(e){}
    await wait(80);

    try{
      btn.focus();
      btn.click();
      console.log(&#x27;[TritoX TM] AgencyZoom Update clicked automatically&#x27;);
      return {ok:true,message:&#x27;Update clicked automatically&#x27;};
    }catch(e){
      console.warn(&#x27;[TritoX TM] Automatic Update click failed:&#x27;,e);
      return {ok:false,message:&#x27;Update click failed&#x27;};
    }
  }

  async function waitFor(getter,timeout){
    const start=Date.now();
    while(Date.now()-start&lt;timeout){
      const value=getter();
      if(value) return value;
      await wait(250);
    }
    return null;
  }

  function findLeadTab(label){
    const wanted=String(label).toLowerCase();
    const match=Array.from(document.querySelectorAll(&#x27;#referral-container a,#referral-container button,#referral-container [role=&quot;tab&quot;],#referral-container li,#referral-container span,#referral-container div&#x27;))
      .find(function(el){return (el.textContent||&#x27;&#x27;).trim().toLowerCase()===wanted;})||null;
    return match ? (match.closest(&#x27;a,button,li,[role=&quot;tab&quot;]&#x27;)||match) : null;
  }

  async function openLeadTab(label){
    const tab=findLeadTab(label);
    if(!tab) return false;
    tab.click();
    await wait(900);
    return true;
  }

  function payloadToFile(payload){
    const parts=String(payload.dataUrl||&#x27;&#x27;).split(&#x27;,&#x27;);
    if(parts.length&lt;2) throw new Error(&#x27;Stored PDF data is incomplete&#x27;);
    const bytes=atob(parts[1]);
    const array=new Uint8Array(bytes.length);
    for(let i=0;i&lt;bytes.length;i++) array[i]=bytes.charCodeAt(i);
    return new File([array],payload.name,{
      type:payload.type||&#x27;application/pdf&#x27;,
      lastModified:payload.lastModified||Date.now()
    });
  }

  function findAgencyZoomFileInput(){
    const selectors=[
      &#x27;#referral-container input[type=&quot;file&quot;]&#x27;,
      &#x27;.agencydocupload_doc input[type=&quot;file&quot;]&#x27;,
      &#x27;#agencyDocUploader input[type=&quot;file&quot;]&#x27;,
      &#x27;input.agencydocupload_doc[type=&quot;file&quot;]&#x27;,
      &#x27;input[type=&quot;file&quot;][multiple]&#x27;,
      &#x27;input[type=&quot;file&quot;]&#x27;
    ];
    for(const selector of selectors){
      const inputs=Array.from(document.querySelectorAll(selector));
      if(inputs.length) return inputs[inputs.length-1];
    }
    return null;
  }

  function pdfNameIsVisible(fileName){
    const wanted=String(fileName||&#x27;&#x27;).toLowerCase();
    const panel=document.getElementById(&#x27;referral-container&#x27;);
    if(!panel || !wanted) return false;
    if((panel.innerText||&#x27;&#x27;).toLowerCase().includes(wanted)) return true;
    return Array.from(panel.querySelectorAll(&#x27;[title],[data-name],[data-file-name],a&#x27;))
      .some(function(el){
        return [el.getAttribute(&#x27;title&#x27;),el.getAttribute(&#x27;data-name&#x27;),el.getAttribute(&#x27;data-file-name&#x27;),el.textContent]
          .some(function(value){return String(value||&#x27;&#x27;).toLowerCase().includes(wanted);});
      });
  }

  function getCurrentLeadId(){
    // The visible &quot;ID: 12345678&quot; is in AgencyZoom&#x27;s lead header, outside
    // #referral-container. Search the complete rendered page first.
    const texts=[
      document.body&amp;&amp;document.body.innerText,
      document.documentElement&amp;&amp;document.documentElement.innerText,
      document.getElementById(&#x27;referral-container&#x27;)&amp;&amp;document.getElementById(&#x27;referral-container&#x27;).innerText
    ];
    for(const text of texts){
      const match=String(text||&#x27;&#x27;).match(/\bID\s*:\s*(\d{5,})\b/i);
      if(match) return match[1];
    }

    const selectors=[
      &#x27;[data-entity-id]&#x27;,&#x27;[data-entityid]&#x27;,&#x27;[data-lead-id]&#x27;,&#x27;[data-leadid]&#x27;,
      &#x27;input[name=&quot;entityId&quot;]&#x27;,&#x27;input[name=&quot;leadId&quot;]&#x27;
    ];
    for(const selector of selectors){
      const el=document.querySelector(selector);
      if(!el) continue;
      const value=el.value||el.getAttribute(&#x27;data-entity-id&#x27;)||el.getAttribute(&#x27;data-entityid&#x27;)||
        el.getAttribute(&#x27;data-lead-id&#x27;)||el.getAttribute(&#x27;data-leadid&#x27;);
      if(/^\d{5,}$/.test(String(value||&#x27;&#x27;))) return String(value);
    }

    // Final fallback: AgencyZoom often embeds the active lead ID in its
    // uploader configuration even when the header has not finished rendering.
    const html=document.documentElement&amp;&amp;document.documentElement.innerHTML||&#x27;&#x27;;
    const configMatch=html.match(/(?:entityId|leadId)[&quot;&#x27;]?\s*[:=]\s*[&quot;&#x27;]?(\d{5,})/i);
    if(configMatch) return configMatch[1];
    return &#x27;&#x27;;
  }

  function normalizePersonName(v){
    let s=String(v||&#x27;&#x27;).toLowerCase();

    // Remove accents when available, then normalize punctuation/hyphens/apostrophes.
    try{ s=s.normalize(&#x27;NFD&#x27;).replace(/[\u0300-\u036f]/g,&#x27;&#x27;); }catch(e){}
    return s
      .replace(/&amp;/g,&#x27; and &#x27;)
      .replace(/[^a-z0-9]+/g,&#x27; &#x27;)
      .trim()
      .replace(/\s+/g,&#x27; &#x27;);
  }

  function nameTokens(v){
    let parts=normalizePersonName(v).split(&#x27; &#x27;).filter(Boolean);

    // Ignore common suffixes for matching: Jr, Sr, II, III, IV, V, Junior, Senior.
    const suffixes=new Set([&#x27;jr&#x27;,&#x27;sr&#x27;,&#x27;ii&#x27;,&#x27;iii&#x27;,&#x27;iv&#x27;,&#x27;v&#x27;,&#x27;junior&#x27;,&#x27;senior&#x27;]);
    while(parts.length&gt;1 &amp;&amp; suffixes.has(parts[parts.length-1])){
      parts.pop();
    }
    return parts;
  }

  function getCurrentLeadName(){
    const text=String(document.body&amp;&amp;document.body.innerText||&#x27;&#x27;);

    let m=text.match(/(?:^|\n)\s*([A-Za-z][A-Za-z .&#x27;\-]{1,80})\s*\n\s*QuoteWizard\s*\|\s*ID\s*:/i);
    if(m) return String(m[1]||&#x27;&#x27;).trim();

    m=text.match(/(?:^|\n)\s*([A-Za-z][A-Za-z .&#x27;\-]{1,80}?)\s+QuoteWizard\s*\|\s*ID\s*:/i);
    if(m) return String(m[1]||&#x27;&#x27;).trim();

    const id=getCurrentLeadId();
    if(id){
      const lines=text.split(/\n+/).map(function(x){return String(x||&#x27;&#x27;).trim();}).filter(Boolean);
      const rx=new RegExp(&#x27;\\bID\\s*:\\s*&#x27;+id+&#x27;\\b&#x27;,&#x27;i&#x27;);
      const pos=lines.findIndex(function(x){return rx.test(x);});
      if(pos&gt;0){
        for(let j=pos-1;j&gt;=Math.max(0,pos-4);j--){
          const candidate=lines[j];
          if(!candidate || /quotewizard|activities|contacts|opportunities|quotes|referral|main/i.test(candidate)) continue;
          if(/^[A-Za-z][A-Za-z .&#x27;\-]{1,80}$/.test(candidate)) return candidate;
        }
      }
    }
    return &#x27;&#x27;;
  }

  function publishCurrentAgencyZoomLead(){
    try{
      const leadId=getCurrentLeadId();
      if(!leadId)return;
      const leadName=getCurrentLeadName();
      GM_setValue(&#x27;tritox_current_az_lead&#x27;,JSON.stringify({
        leadId:String(leadId),
        leadName:String(leadName||&#x27;&#x27;),
        ts:Date.now()
      }));
    }catch(e){}
  }
  publishCurrentAgencyZoomLead();
  setInterval(publishCurrentAgencyZoomLead,300);

  function firstNameCandidates(parts){
    const out=new Set();
    if(!parts.length) return out;
    out.add(parts[0]);

    // Handles PDF text splits such as &quot;Je ff&quot; =&gt; &quot;jeff&quot;.
    if(parts.length&gt;=2 &amp;&amp; (parts[0].length&lt;=2 || parts[1].length&lt;=2)){
      out.add(parts[0]+parts[1]);
    }

    // Handles hyphenated/compound first names when one source removes punctuation:
    // Mary-Anne &lt;=&gt; Maryanne.
    if(parts.length&gt;=3){
      out.add(parts[0]+parts[1]);
    }
    return out;
  }

  function lastNameCandidates(parts){
    const out=new Set();
    if(!parts.length) return out;
    out.add(parts[parts.length-1]);

    // Handles split/compound/hyphenated surnames:
    // Van Dyke &lt;=&gt; VanDyke, Smith-Jones &lt;=&gt; SmithJones.
    if(parts.length&gt;=3){
      out.add(parts[parts.length-2]+parts[parts.length-1]);
    }
    return out;
  }

  function tokenSetIntersects(a,b){
    for(const x of a){ if(b.has(x)) return true; }
    return false;
  }

  function namesMatch(pdfName,leadName){
    const aa=nameTokens(pdfName), bb=nameTokens(leadName);
    if(!aa.length || !bb.length) return null;

    const a=aa.join(&#x27; &#x27;);
    const b=bb.join(&#x27; &#x27;);
    if(a===b) return true;

    // Capitalization, spaces, hyphens, apostrophes and PDF word-splitting disappear
    // in this form. Examples: &quot;Je ff&quot; == &quot;Jeff&quot;, &quot;O&#x27;Connor&quot; == &quot;OConnor&quot;.
    const compactA=aa.join(&#x27;&#x27;);
    const compactB=bb.join(&#x27;&#x27;);
    if(compactA===compactB) return true;

    const firstA=firstNameCandidates(aa);
    const firstB=firstNameCandidates(bb);
    const lastA=lastNameCandidates(aa);
    const lastB=lastNameCandidates(bb);

    // Main real-world rule: same first + same last. Middle names/initials are
    // intentionally ignored, so 2-name, 3-name and 4-name forms can match.
    if(tokenSetIntersects(firstA,firstB) &amp;&amp; tokenSetIntersects(lastA,lastB)){
      return true;
    }

    // First-name initial/prefix support, but only when the surname matches.
    // Examples: &quot;J Smith&quot; &lt;=&gt; &quot;Jeff Smith&quot;, &quot;Les Modrow&quot; &lt;=&gt; &quot;Lester Modrow&quot;.
    if(tokenSetIntersects(lastA,lastB)){
      for(const fa of firstA){
        for(const fb of firstB){
          if((fa.length===1 &amp;&amp; fb.startsWith(fa)) ||
             (fb.length===1 &amp;&amp; fa.startsWith(fb)) ||
             (Math.min(fa.length,fb.length)&gt;=3 &amp;&amp;
               (fa.startsWith(fb)||fb.startsWith(fa)))){
            return true;
          }
        }
      }
    }

    return false;
  }


  function filenameFallbackMatches(data,leadName){
    try{
      const filename=String(data&amp;&amp;data._filename||&#x27;&#x27;).replace(/\.pdf$/i,&#x27;&#x27;).trim();
      if(!filename || !leadName) return false;

      // Recognize our normal saved-file pattern, e.g.
      // Galloway-townsend_Auto_09282026.pdf
      // Siboloski_Auto_09252026.pdf
      // Smith_Bundle_09252026.pdf
      const m=filename.match(/^(.*?)[_\-\s]+(?:auto|bundle|home)(?:[_\-\s]+(\d{8}))?$/i);
      if(!m) return false;

      const fileCustomer=String(m[1]||&#x27;&#x27;).trim();
      if(!fileCustomer) return false;

      const leadParts=nameTokens(leadName);
      if(!leadParts.length) return false;

      const leadLast=lastNameCandidates(leadParts);
      const fileParts=nameTokens(fileCustomer);
      if(!fileParts.length) return false;

      const fileCompact=fileParts.join(&#x27;&#x27;);
      if(!fileCompact) return false;

      // Exact surname / compound-surname comparison only.
      // This is used only when PDF text extraction fell back to the filename.
      for(const last of leadLast){
        if(fileCompact===String(last||&#x27;&#x27;).replace(/\s+/g,&#x27;&#x27;)) return true;
      }
      return false;
    }catch(e){
      return false;
    }
  }

  function bindDataToCurrentLeadId(data){
    try{
      if(!data || !data._name) return data;

      const leadId=getCurrentLeadId();
      const leadName=getCurrentLeadName();
      let currentNameMatch=namesMatch(data._name,leadName);

      // If PDF.js failed to read &quot;Prepared for&quot; and QC fell back to a filename
      // such as Galloway-townsend_Auto_09282026, allow an exact surname match
      // from that filename to establish the AgencyZoom Lead ID.
      if(currentNameMatch!==true &amp;&amp; filenameFallbackMatches(data,leadName)){
        currentNameMatch=true;
        console.log(&#x27;[TritoX TM] Filename fallback matched AgencyZoom surname:&#x27;,
          data._filename,&#x27;=&gt;&#x27;,leadName);
      }

      let existingId=String(data._leadId||&#x27;&#x27;).trim();
      const boundName=String(data._leadNameBound||&#x27;&#x27;).trim();

      if(existingId){
        const boundNameMatch=boundName ? namesMatch(data._name,boundName) : null;

        // Recovery for v4.46.11/v4.46.12 stale bindings:
        // if the stored bound name belongs to another PDF/customer, discard it.
        if(boundName &amp;&amp; boundNameMatch===false){
          console.warn(&#x27;[TritoX TM] Clearing stale AgencyZoom ID binding:&#x27;,
            data._name,&#x27;was bound to&#x27;,existingId,boundName);
          data._leadId=&#x27;&#x27;;
          data._leadNameBound=&#x27;&#x27;;
          existingId=&#x27;&#x27;;
        }

        // If the current lead name safely matches this PDF but the existing ID
        // points elsewhere, rebind to the current lead. This repairs stale IDs
        // without requiring the PDF to be processed again.
        if(existingId &amp;&amp; leadId &amp;&amp; existingId!==String(leadId) &amp;&amp; currentNameMatch===true){
          console.warn(&#x27;[TritoX TM] Rebinding stale AgencyZoom ID:&#x27;,
            existingId,&#x27;-&gt;&#x27;,leadId,&#x27;for&#x27;,data._name);
          data._leadId=String(leadId);
          data._leadNameBound=String(leadName||&#x27;&#x27;);
          GM_setValue(&#x27;tritox_az_data&#x27;,JSON.stringify(data));
          return data;
        }

        // A valid existing binding remains authoritative.
        if(existingId) return data;
      }

      if(!leadId) return data;

      // First-time binding. The robust matcher accepts capitalization,
      // punctuation/hyphens, middle names/initials, suffixes, and PDF word splits.
      if(currentNameMatch===true){
        data._leadId=String(leadId);
        data._leadNameBound=String(leadName||&#x27;&#x27;);
        GM_setValue(&#x27;tritox_az_data&#x27;,JSON.stringify(data));
        console.log(&#x27;[TritoX TM] PDF bound to AgencyZoom lead ID:&#x27;,
          data._name,&#x27;=&gt;&#x27;,leadId,leadName);
      }
      return data;
    }catch(e){
      console.warn(&#x27;[TritoX TM] Lead ID binding skipped:&#x27;,e);
      return data;
    }
  }


  function validateLeadIdentity(data){
    data=bindDataToCurrentLeadId(data);

    const pdfName=String(data&amp;&amp;data._name||&#x27;&#x27;).trim();
    const leadName=getCurrentLeadName();
    const leadId=getCurrentLeadId();
    const expectedId=String(data&amp;&amp;data._leadId||&#x27;&#x27;).trim();

    let nameMatch=namesMatch(pdfName,leadName);
    if(nameMatch!==true &amp;&amp; filenameFallbackMatches(data,leadName)){
      nameMatch=true;
    }
    const idMatch=(expectedId &amp;&amp; leadId) ? expectedId===leadId : null;

    // ID is authoritative after the first safe binding.
    // If no expected ID exists yet, use the name guard as the fallback.
    const mismatch=(idMatch!==null) ? (idMatch===false) : (nameMatch===false);

    return {
      pdfName:pdfName,
      leadName:leadName,
      leadId:leadId,
      expectedId:expectedId,
      nameMatch:nameMatch,
      idMatch:idMatch,
      mismatch:mismatch
    };
  }


  function agencyZoomUploadName(originalName){
    const name=String(originalName||&#x27;quote.pdf&#x27;);

    // Rename ONLY the AgencyZoom upload. Keep the original local/cached name
    // unchanged so PDF matching still works for multi-quote processing.
    //
    // Example:
    //   Wolthuis_Auto_09252026.pdf -&gt; Wolthuis_Auto.pdf
    //
    // Only remove a trailing _MMDDYYYY date immediately before .pdf.
    const m=name.match(/^(.*)_((?:0[1-9]|1[0-2])(?:0[1-9]|[12]\d|3[01])(?:19|20)\d{2})(\.pdf)$/i);
    if(!m) return name;

    const trimmed=String(m[1]||&#x27;&#x27;).replace(/[_\-\s]+$/,&#x27;&#x27;);
    return (trimmed||&#x27;quote&#x27;)+m[3];
  }

  async function uploadPdfDirect(file,leadId){
    const pageWindow=typeof unsafeWindow!==&#x27;undefined&#x27;?unsafeWindow:window;
    const jq=pageWindow.jQuery;
    if(!jq || typeof jq.ajax!==&#x27;function&#x27;) throw new Error(&#x27;AgencyZoom uploader session was not ready&#x27;);

    const uploadName=agencyZoomUploadName(file.name);

    const query=new URLSearchParams({
      docType:&#x27;undefined&#x27;,
      fileName:uploadName,
      docuSign:&#x27;0&#x27;
    });
    // Create the upload File with the SHORTENED AgencyZoom filename only.
    // The cached/original PDF name remains unchanged.
    const bytes=await file.arrayBuffer();
    const pageFile=new pageWindow.File([bytes],uploadName,{
      type:file.type||&#x27;application/pdf&#x27;,
      lastModified:file.lastModified||Date.now()
    });
    const form=new pageWindow.FormData();
    form.append(&#x27;contacts&#x27;,&#x27;[]&#x27;);
    form.append(&#x27;emailSubject&#x27;,&#x27;&#x27;);
    form.append(&#x27;emailBody&#x27;,&#x27;&#x27;);
    form.append(&#x27;entityId&#x27;,String(leadId));
    form.append(&#x27;linkToType&#x27;,&#x27;lead&#x27;);
    form.append(&#x27;files[]&#x27;,pageFile,uploadName);

    console.log(&#x27;[TritoX TM] AgencyZoom PDF name:&#x27;,file.name,&#x27;-&gt;&#x27;,uploadName);

    return new Promise(function(resolve,reject){
      jq.ajax({
        url:&#x27;/lead/doc?&#x27;+query.toString(),
        type:&#x27;POST&#x27;,
        data:form,
        processData:false,
        contentType:false,
        cache:false,
        success:function(result){
          if(result &amp;&amp; result.docName &amp;&amp; String(result.docName)!==uploadName){
            reject(new Error(&#x27;AgencyZoom returned a different filename&#x27;));
            return;
          }
          resolve(result);
        },
        error:function(xhr){
          let detail=&#x27;HTTP &#x27;+(xhr&amp;&amp;xhr.status||&#x27;error&#x27;);
          try{
            const body=xhr.responseJSON||JSON.parse(xhr.responseText||&#x27;{}&#x27;);
            detail=body.message||body.error||detail;
          }catch(e){}
          reject(new Error(detail));
        }
      });
    });
  }

  async function attachPdfFast(data){
    if(!data._filename) return {ok:false,message:&#x27;PDF filename was not transferred&#x27;};
    const stored=GM_getValue(pdfStorageKey(data._filename),&#x27;&#x27;);
    if(!stored) return {ok:false,message:&#x27;PDF was not cached — select it again in Aaron QC&#x27;};

    let payload;
    try{payload=JSON.parse(stored);}catch(e){return {ok:false,message:&#x27;Stored PDF could not be read&#x27;};}
    if(payload.name!==data._filename) return {ok:false,message:&#x27;PDF filename mismatch — attachment stopped&#x27;};

    const file=payloadToFile(payload);
    const leadId=getCurrentLeadId();
    if(!leadId) return {ok:false,message:&#x27;AgencyZoom lead ID was not found&#x27;};

    // Fast mode: trust AgencyZoom&#x27;s successful upload response instead of
    // navigating to Files and waiting for the filename to repaint.
    try{
      const result=await uploadPdfDirect(file,leadId);
      if(result===undefined || result===null) return {ok:true,message:&#x27;PDF upload accepted by AgencyZoom&#x27;};
      return {ok:true,message:&#x27;PDF uploaded to AgencyZoom&#x27;};
    }catch(err){
      console.error(&#x27;[TritoX TM] Fast PDF upload failed:&#x27;,err);
      return {ok:false,message:&#x27;AgencyZoom rejected PDF upload: &#x27;+String(err.message||err)};
    }
  }

  async function attachPdfToLead(data){
    if(!data._filename) return {ok:false,message:&#x27;PDF filename was not transferred&#x27;};
    const stored=GM_getValue(pdfStorageKey(data._filename),&#x27;&#x27;);
    if(!stored) return {ok:false,message:&#x27;PDF was not cached — select it again in Aaron QC&#x27;};

    let payload;
    try{payload=JSON.parse(stored);}catch(e){return {ok:false,message:&#x27;Stored PDF could not be read&#x27;};}
    if(payload.name!==data._filename) return {ok:false,message:&#x27;PDF filename mismatch — attachment stopped&#x27;};

    const file=payloadToFile(payload);
    const leadId=getCurrentLeadId();
    if(!leadId) return {ok:false,message:&#x27;AgencyZoom lead ID was not found&#x27;};

    // Upload through AgencyZoom&#x27;s own multipart endpoint.
    await openLeadTab(&#x27;Files&#x27;);
    await waitFor(function(){return document.getElementById(&#x27;referral-container&#x27;);},5000);

    try{
      await uploadPdfDirect(file,leadId);
    }catch(err){
      console.error(&#x27;[TritoX TM] Direct PDF upload failed:&#x27;,err);
      return {ok:false,message:&#x27;AgencyZoom rejected PDF upload: &#x27;+String(err.message||err)};
    }

    // IMPORTANT: a 200/upload object is not treated as success by itself.
    // Confirm the actual filename appears in the lead&#x27;s Files UI.
    let visible=await waitFor(function(){
      return pdfNameIsVisible(agencyZoomUploadName(file.name)) ? true : null;
    },5000);

    // AgencyZoom sometimes does not repaint the Files list immediately.
    // Force a tab re-render once, then verify again.
    if(!visible){
      await openLeadTab(&#x27;Main&#x27;);
      await wait(500);
      await openLeadTab(&#x27;Files&#x27;);
      visible=await waitFor(function(){
        return pdfNameIsVisible(agencyZoomUploadName(file.name)) ? true : null;
      },7000);
    }

    if(visible){
      return {ok:true,message:&#x27;PDF verified in AgencyZoom Files&#x27;};
    }

    console.warn(&#x27;[TritoX TM] Upload response received but file was not visible:&#x27;,agencyZoomUploadName(file.name));
    return {
      ok:false,
      message:&#x27;PDF upload was not verified in Files — please check/attach manually&#x27;
    };
  }

  function fillText(id, val){
    if(!val &amp;&amp; val !== 0) return;
    const el = document.getElementById(&#x27;customfields-&#x27; + id);
    if(!el) return;
    try{
      el.focus();
      const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,&#x27;value&#x27;).set;
      setter.call(el, String(val));
      el.dispatchEvent(new Event(&#x27;focus&#x27;,{bubbles:true}));
      el.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
      el.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
      el.dispatchEvent(new KeyboardEvent(&#x27;keydown&#x27;,{bubbles:true}));
      el.dispatchEvent(new KeyboardEvent(&#x27;keyup&#x27;,{bubbles:true}));
      el.blur();
      el.dispatchEvent(new Event(&#x27;blur&#x27;,{bubbles:true}));
    }catch(e){}
  }

  function fillSelect(id, val){
    if(!val) return;
    const el = document.getElementById(&#x27;customfields-&#x27; + id);
    if(!el) return;
    try{
      const setter = Object.getOwnPropertyDescriptor(window.HTMLSelectElement.prototype,&#x27;value&#x27;).set;
      setter.call(el, String(val));
      el.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
    }catch(e){}
  }

  function quoteTagNames(data){
    const isBundle = typeof data._isBundle === &#x27;boolean&#x27;
      ? data._isBundle
      : !!(data.home_coverage_a || data.home_annual);
    const isHighPrice = !!(data &amp;&amp; (data._highPrice === true || data.high_price === true || String(data._highPrice||&#x27;&#x27;).toLowerCase()===&#x27;true&#x27;));
    const names=[isHighPrice ? &#x27;PRICE TOO HIGH&#x27; : &#x27;Ready to send&#x27;];
    if(isBundle) names.push(&#x27;Home is quoted&#x27;);
    return names;
  }

  function resolveStarTagName(select,data){
    const raw=String((data&amp;&amp;data.star_rating)||&#x27;&#x27;).trim();
    if(!raw || !select) return &#x27;&#x27;;
    const options=Array.from(select.options||[]).filter(function(o){return !o.disabled;});
    const text=function(o){return String(o.textContent||&#x27;&#x27;).replace(/\s+/g,&#x27; &#x27;).trim();};
    const starMatch=raw.match(/(?:^|\b)([123])(?:\s*stars?)?(?:\b|$)/i);
    const isBW=/\bbw\b|bristol\s*west/i.test(raw);

    if(starMatch){
      const n=starMatch[1];
      const patterns=[
        new RegExp(&#x27;^&#x27;+n+&#x27;\\s*stars?$&#x27;, &#x27;i&#x27;),
        new RegExp(&#x27;^star\\s*&#x27;+n+&#x27;$&#x27;, &#x27;i&#x27;),
        new RegExp(&#x27;^&#x27;+n+&#x27;$&#x27;, &#x27;i&#x27;)
      ];
      for(const re of patterns){
        const hit=options.find(function(o){return re.test(text(o));});
        if(hit) return text(hit);
      }
    }
    if(isBW){
      const hit=options.find(function(o){return /^(?:BW|Bristol\s*West)$/i.test(text(o));});
      if(hit) return text(hit);
    }
    return &#x27;&#x27;;
  }

  async function applyQuoteTags(data){
    let toggle=null;
    let opened=false;
    try{
      const names=quoteTagNames(data);
      const norm=function(v){return String(v||&#x27;&#x27;).replace(/\s+/g,&#x27; &#x27;).trim().toLowerCase();};
      const wanted=names.map(norm);
      const pageWindow=typeof unsafeWindow!==&#x27;undefined&#x27;?unsafeWindow:window;
      const jq=pageWindow.jQuery;
      const PageEvent=pageWindow.Event||Event;

      function isVisible(el){
        if(!el) return false;
        const r=el.getBoundingClientRect();
        const cs=pageWindow.getComputedStyle? pageWindow.getComputedStyle(el):window.getComputedStyle(el);
        return !!(el.getClientRects().length &amp;&amp; r.width&gt;0 &amp;&amp; r.height&gt;0 &amp;&amp; cs.display!==&#x27;none&#x27; &amp;&amp; cs.visibility!==&#x27;hidden&#x27;);
      }

      function openAddTagPanelIfNeeded(){
        // If the Add Tag panel is closed, open it first. AgencyZoom has used
        // different button markup across builds, so match accessible labels,
        // titles and nearby tag-related controls rather than one brittle selector.
        const selectors=[
          &#x27;[aria-label*=&quot;tag&quot; i]&#x27;,&#x27;[title*=&quot;tag&quot; i]&#x27;,&#x27;[data-original-title*=&quot;tag&quot; i]&#x27;,
          &#x27;button&#x27;,&#x27;a&#x27;,&#x27;[role=&quot;button&quot;]&#x27;
        ];
        const seen=new Set();
        const candidates=[];
        selectors.forEach(function(sel){
          document.querySelectorAll(sel).forEach(function(el){
            if(seen.has(el) || !isVisible(el)) return;
            seen.add(el);
            const txt=norm((el.getAttribute(&#x27;aria-label&#x27;)||&#x27;&#x27;)+&#x27; &#x27;+(el.getAttribute(&#x27;title&#x27;)||&#x27;&#x27;)+&#x27; &#x27;+(el.getAttribute(&#x27;data-original-title&#x27;)||&#x27;&#x27;)+&#x27; &#x27;+(el.textContent||&#x27;&#x27;));
            if(txt===&#x27;add tag&#x27; || txt.includes(&#x27;add tag&#x27;) || txt===&#x27;tags&#x27; || txt===&#x27;tag&#x27;) candidates.push(el);
          });
        });
        if(candidates.length){
          try{ candidates[0].click(); return true; }catch(e){}
        }
        return false;
      }

      function collectTagCandidates(){
        return Array.from(document.querySelectorAll(&#x27;select&#x27;)).map(function(select,index){
        const options=Array.from(select.options||[]);
        const optionNames=options.map(function(o){return norm(o.textContent);});
        if(!wanted.every(function(name){return optionNames.includes(name);})) return null;
        const wrapper=select.closest(&#x27;.bootstrap-select&#x27;);
        const button=wrapper&amp;&amp;wrapper.querySelector(&#x27;button.dropdown-toggle&#x27;);
        const selectedNames=Array.from(select.selectedOptions||[]).map(function(o){return norm(o.textContent);}).filter(Boolean);
        const identity=norm((select.id||&#x27;&#x27;)+&#x27; &#x27;+(select.name||&#x27;&#x27;)+&#x27; &#x27;+(select.className||&#x27;&#x27;));
        let score=0;
        if(select.closest(&#x27;#referral-container&#x27;)) score+=1000;
        if(select.name===&#x27;tags[]&#x27;) score+=1000;
        if(identity.includes(&#x27;tag&#x27;)) score+=600;
        if(select.multiple) score+=400;
        if(isVisible(wrapper||select)) score+=700;
        if(button &amp;&amp; isVisible(button)) score+=400;
        if(selectedNames.length) score+=500;
        if(selectedNames.includes(&#x27;quote team&#x27;)) score+=2000;
        score+=index/10000;
        return {select,wrapper,button,selectedNames,score};
        }).filter(Boolean).sort(function(a,b){return b.score-a.score;});
      }

      let candidates=collectTagCandidates();
      if(!candidates.length){
        openAddTagPanelIfNeeded();
        await wait(120);
        candidates=collectTagCandidates();
      }

      if(!candidates.length) throw new Error(&#x27;Lead tag field was not found — open Add Tag and add &#x27;+names.join(&#x27; + &#x27;)+&#x27; manually&#x27;);
      const chosen=candidates[0];
      const select=chosen.select;
      const wrapper=chosen.wrapper;
      toggle=chosen.button;
      if(!wrapper || !toggle) throw new Error(&#x27;Lead tag dropdown UI was not found&#x27;);

      // Add the matching Star/BW TAG using the exact option text available in
      // AgencyZoom. This is separate from filling the Star 1-3 custom field.
      const starTagName=resolveStarTagName(select,data);
      if(starTagName &amp;&amp; !names.some(function(n){return norm(n)===norm(starTagName);})) names.push(starTagName);

      const beforeValues=Array.from(select.selectedOptions||[]).map(function(o){return String(o.value);});

      function syncTagSelect(changedIndex){
        try{ select.dispatchEvent(new PageEvent(&#x27;input&#x27;,{bubbles:true})); }catch(e){}
        try{ select.dispatchEvent(new PageEvent(&#x27;change&#x27;,{bubbles:true})); }catch(e){}
        if(jq){
          try{ jq(select).trigger(&#x27;change&#x27;); }catch(e){}
          if(Number.isInteger(changedIndex)){
            try{ jq(select).trigger(&#x27;changed.bs.select&#x27;,[changedIndex,true,null]); }catch(e){}
          }
          try{ if(typeof jq(select).selectpicker===&#x27;function&#x27;) jq(select).selectpicker(&#x27;refresh&#x27;); }catch(e){}
        }
      }

      if(toggle.getAttribute(&#x27;aria-expanded&#x27;)!==&#x27;true&#x27;){
        toggle.click();
        opened=true;
        await wait(25);
      }

      const listId=toggle.getAttribute(&#x27;aria-owns&#x27;)||toggle.getAttribute(&#x27;aria-controls&#x27;);
      const list=(listId&amp;&amp;document.getElementById(listId))||wrapper;

      for(const name of names){
        const options=Array.from(select.options||[]);
        const option=options.find(function(o){return norm(o.textContent)===norm(name);});
        if(!option) throw new Error(&#x27;Tag unavailable: &#x27;+name);
        if(option.disabled) throw new Error(&#x27;Tag disabled: &#x27;+name);
        const optionIndex=options.indexOf(option);

        if(!option.selected){
          let item=list.querySelector(&#x27;li[data-original-index=&quot;&#x27;+optionIndex+&#x27;&quot;] a, li[data-original-index=&quot;&#x27;+optionIndex+&#x27;&quot;] [role=&quot;option&quot;]&#x27;);
          if(!item){
            item=Array.from(list.querySelectorAll(&#x27;a,[role=&quot;option&quot;],button,li&#x27;)).find(function(el){
              return norm(el.textContent)===norm(name) &amp;&amp; el.getAttribute(&#x27;aria-disabled&#x27;)!==&#x27;true&#x27;;
            })||null;
          }
          if(!item) throw new Error(&#x27;Tag menu item not found: &#x27;+name);

          // Real option click first.
          try{ item.click(); }catch(e){}
          await wait(20);

          // Keep the underlying select definitely in sync with what the UI shows.
          if(!option.selected) option.selected=true;
          syncTagSelect(optionIndex);
          await wait(20);
        }else{
          // Even for an already-selected option, sync AgencyZoom&#x27;s model once.
          syncTagSelect(optionIndex);
        }
      }

      // One final model sync before Save. This is the key v4.21 change: the
      // tags could look selected in Bootstrap while AgencyZoom&#x27;s form model was
      // still unchanged, causing Save to do nothing.
      syncTagSelect(null);

      if(opened &amp;&amp; toggle.getAttribute(&#x27;aria-expanded&#x27;)===&#x27;true&#x27;){
        toggle.click();
        opened=false;
        await wait(20);
      }

      // Find the EXACT Add Tag container by walking upward from this select.
      // AgencyZoom&#x27;s current Add Tag UI is not always a Bootstrap .modal.
      function findTagDialog(){
        let el=select;
        while(el &amp;&amp; el!==document.body){
          if(isVisible(el)){
            const txt=norm(el.textContent);
            if(txt.includes(&#x27;add tag&#x27;) &amp;&amp; txt.includes(&#x27;choose tags&#x27;)){
              const save=Array.from(el.querySelectorAll(&#x27;button,input[type=&quot;button&quot;],input[type=&quot;submit&quot;],a,[role=&quot;button&quot;]&#x27;)).find(function(b){
                return isVisible(b) &amp;&amp; !b.disabled &amp;&amp; b.getAttribute(&#x27;aria-disabled&#x27;)!==&#x27;true&#x27; &amp;&amp; norm(b.value||b.textContent||b.getAttribute(&#x27;aria-label&#x27;))===&#x27;save&#x27;;
              });
              if(save) return {root:el,saveBtn:save};
            }
          }
          el=el.parentElement;
        }

        // Fallback: smallest visible page container that contains Add Tag,
        // Choose tags and a visible Save button.
        const saves=Array.from(document.querySelectorAll(&#x27;button,input[type=&quot;button&quot;],input[type=&quot;submit&quot;],a,[role=&quot;button&quot;]&#x27;)).filter(function(b){
          return isVisible(b) &amp;&amp; !b.disabled &amp;&amp; b.getAttribute(&#x27;aria-disabled&#x27;)!==&#x27;true&#x27; &amp;&amp; norm(b.value||b.textContent||b.getAttribute(&#x27;aria-label&#x27;))===&#x27;save&#x27;;
        });
        for(const saveBtn of saves){
          let root=saveBtn.parentElement;
          for(let i=0;root &amp;&amp; root!==document.body &amp;&amp; i&lt;8;i++,root=root.parentElement){
            const txt=norm(root.textContent);
            if(txt.includes(&#x27;add tag&#x27;) &amp;&amp; txt.includes(&#x27;choose tags&#x27;)) return {root,saveBtn};
          }
        }
        return null;
      }

      const tagDialog=findTagDialog();
      if(!tagDialog) throw new Error(&#x27;Tags selected, but the Add Tag Save button was not found&#x27;);
      const dialog=tagDialog.root;
      let saveBtn=tagDialog.saveBtn;

      // Make sure AgencyZoom receives the final selected values immediately
      // before the Save handler reads them.
      syncTagSelect(null);
      await wait(20);

      function clickSave(btn){
        if(!btn) return false;
        try{ btn.focus(); }catch(e){}
        try{ btn.click(); return true; }catch(e){}
        if(jq){
          try{ jq(btn).trigger(&#x27;click&#x27;); return true; }catch(e){}
        }
        return false;
      }

      let clicked=clickSave(saveBtn);
      if(!clicked) throw new Error(&#x27;AgencyZoom tag Save button could not be clicked&#x27;);

      // Fast confirmation: most saves close the Add Tag panel in under 1 second.
      let closed=await waitFor(function(){ return !isVisible(dialog) ? true : null; },150);

      if(!closed){
        // If click alone did not invoke the form submission, submit the exact
        // form containing the Save button. This preserves AgencyZoom validation.
        const form=saveBtn.closest(&#x27;form&#x27;);
        if(form){
          try{
            if(typeof form.requestSubmit===&#x27;function&#x27;) form.requestSubmit(saveBtn);
            else form.dispatchEvent(new PageEvent(&#x27;submit&#x27;,{bubbles:true,cancelable:true}));
          }catch(e){console.warn(&#x27;[TritoX TM] Tag form submit fallback:&#x27;,e);}
        }else if(jq){
          try{ jq(saveBtn).trigger(&#x27;click&#x27;); }catch(e){}
        }
        closed=await waitFor(function(){ return !isVisible(dialog) ? true : null; },200);
      }

      if(!closed) throw new Error(&#x27;Tags are selected, but AgencyZoom did not save/close the Add Tag window&#x27;);

      // Preserve existing tags.
      const afterValues=Array.from(select.selectedOptions||[]).map(function(o){return String(o.value);});
      if(!beforeValues.every(function(v){return afterValues.includes(v);})){ 
        throw new Error(&#x27;Existing tags changed; review the lead tags&#x27;);
      }

      return {ok:true,message:&#x27;Tags saved automatically: &#x27;+names.join(&#x27; + &#x27;)+(data.star_rating &amp;&amp; !starTagName?&#x27; (Star tag option not found)&#x27;:&#x27;&#x27;)};
    }catch(error){
      console.error(&#x27;[TritoX TM] Quote tags:&#x27;,error);
      return {ok:false,message:String(error.message||error)};
    }finally{
      if(opened &amp;&amp; toggle &amp;&amp; toggle.getAttribute(&#x27;aria-expanded&#x27;)===&#x27;true&#x27;) toggle.click();
    }
  }

  function findControlByLabel(labelText){
    const wanted=String(labelText||&#x27;&#x27;).toLowerCase().replace(/[^a-z0-9]+/g,&#x27; &#x27;).trim();
    const labels=Array.from(document.querySelectorAll(&#x27;label,.control-label,[class*=&quot;label&quot;]&#x27;));
    for(const lab of labels){
      const txt=String(lab.textContent||&#x27;&#x27;).toLowerCase().replace(/[^a-z0-9]+/g,&#x27; &#x27;).trim();
      if(txt!==wanted &amp;&amp; !txt.startsWith(wanted)) continue;
      if(lab.htmlFor){
        const byFor=document.getElementById(lab.htmlFor);
        if(byFor &amp;&amp; /^(INPUT|SELECT|TEXTAREA)$/.test(byFor.tagName)) return byFor;
      }
      const containers=[lab.parentElement,lab.closest(&#x27;.form-group&#x27;),lab.closest(&#x27;.az-form-group&#x27;),lab.closest(&#x27;[class*=&quot;form-group&quot;]&#x27;)].filter(Boolean);
      for(const c of containers){
        const el=c.querySelector(&#x27;input,select,textarea&#x27;);
        if(el) return el;
      }
    }
    return null;
  }

  function setAnyControl(el,value){
    if(!el || value===undefined || value===null || String(value)===&#x27;&#x27;) return false;
    const wanted=String(value).trim();
    try{
      if(el.tagName===&#x27;SELECT&#x27;){
        const options=Array.from(el.options||[]);
        let opt=options.find(function(o){return String(o.value).trim().toLowerCase()===wanted.toLowerCase();});
        if(!opt) opt=options.find(function(o){return String(o.textContent||&#x27;&#x27;).trim().toLowerCase()===wanted.toLowerCase();});
        if(!opt &amp;&amp; /^[123]$/.test(wanted)) opt=options.find(function(o){return new RegExp(&#x27;^\\s*&#x27;+wanted+&#x27;(?:\\s|$)&#x27;).test(String(o.textContent||&#x27;&#x27;));});
        if(!opt &amp;&amp; /^bw$/i.test(wanted)) opt=options.find(function(o){return /\\bBW\\b|Bristol West/i.test(String(o.textContent||&#x27;&#x27;));});
        if(!opt) return false;
        el.value=opt.value;
        opt.selected=true;
        el.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
        el.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
        return true;
      }
      const proto=el.tagName===&#x27;TEXTAREA&#x27;?window.HTMLTextAreaElement.prototype:window.HTMLInputElement.prototype;
      const desc=Object.getOwnPropertyDescriptor(proto,&#x27;value&#x27;);
      if(desc&amp;&amp;desc.set) desc.set.call(el,wanted); else el.value=wanted;
      el.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
      el.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
      el.dispatchEvent(new Event(&#x27;blur&#x27;,{bubbles:true}));
      return true;
    }catch(e){console.warn(&#x27;[TritoX TM] Could not fill&#x27;,labelText,value,e);return false;}
  }

  function findStarControl(){
    // First use the visible field label.
    let el=findControlByLabel(&#x27;Star 1-3&#x27;) || findControlByLabel(&#x27;Star 1 - 3&#x27;) || findControlByLabel(&#x27;Star&#x27;);
    if(el &amp;&amp; el.tagName===&#x27;SELECT&#x27;) return el;

    // AgencyZoom sometimes renders the visible Bootstrap control separately
    // from its real &lt;select&gt;. Find a select in the same field container.
    const labels=Array.from(document.querySelectorAll(&#x27;label,.control-label,[class*=&quot;label&quot;]&#x27;));
    for(const lab of labels){
      const txt=String(lab.textContent||&#x27;&#x27;).toLowerCase().replace(/[^a-z0-9]+/g,&#x27; &#x27;).trim();
      if(!txt.includes(&#x27;star&#x27;)) continue;
      let c=lab.closest(&#x27;.form-group,[class*=&quot;form-group&quot;],.row,.col-md-6,.col-sm-6&#x27;) || lab.parentElement;
      if(!c) continue;
      const sel=c.querySelector(&#x27;select&#x27;);
      if(sel) return sel;
    }

    // Final fallback: identify the unique select whose options look like
    // 1/2/3 stars or BW. This avoids relying on a brittle AgencyZoom field id.
    const candidates=Array.from(document.querySelectorAll(&#x27;select&#x27;)).filter(function(sel){
      const text=Array.from(sel.options||[]).map(function(o){return String(o.textContent||&#x27;&#x27;).trim();}).join(&#x27; | &#x27;);
      const has1=/(^|\|)\s*1(?:\s*star)?\s*(\||$)/i.test(text);
      const has2=/(^|\|)\s*2(?:\s*stars?)?\s*(\||$)/i.test(text);
      const has3=/(^|\|)\s*3(?:\s*stars?)?\s*(\||$)/i.test(text);
      const hasBW=/\bBW\b|Bristol West/i.test(text);
      return has1 &amp;&amp; has2 &amp;&amp; has3 &amp;&amp; hasBW;
    });
    return candidates.length===1 ? candidates[0] : (candidates[0]||el||null);
  }

  function setStarControl(value){
    if(value===undefined || value===null || String(value).trim()===&#x27;&#x27;) return false;
    const wanted=String(value).trim();
    const el=findStarControl();
    if(!el) return false;
    if(el.tagName!==&#x27;SELECT&#x27;) return setAnyControl(el,wanted);

    try{
      const options=Array.from(el.options||[]);
      let opt=options.find(function(o){return String(o.value||&#x27;&#x27;).trim().toLowerCase()===wanted.toLowerCase();});
      if(!opt &amp;&amp; /^[123]$/.test(wanted)){
        opt=options.find(function(o){
          const t=String(o.textContent||&#x27;&#x27;).trim();
          return new RegExp(&#x27;^&#x27;+wanted+&#x27;(?:\s*stars?)?$&#x27;, &#x27;i&#x27;).test(t) || new RegExp(&#x27;^&#x27;+wanted+&#x27;(?:\s|$)&#x27;).test(t);
        });
      }
      if(!opt &amp;&amp; /^bw$/i.test(wanted)) opt=options.find(function(o){return /\bBW\b|Bristol West/i.test(String(o.textContent||&#x27;&#x27;));});
      if(!opt) return false;

      // Preserve normal AgencyZoom behavior and refresh Bootstrap-select UI.
      Array.from(el.options||[]).forEach(function(o){o.selected=(o===opt);});
      el.value=opt.value;
      el.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
      el.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
      const pageWindow=typeof unsafeWindow!==&#x27;undefined&#x27;?unsafeWindow:window;
      const jq=pageWindow.jQuery;
      if(jq){
        try{
          const $el=jq(el);
          $el.val(opt.value).trigger(&#x27;change&#x27;);
          if(typeof $el.selectpicker===&#x27;function&#x27;) $el.selectpicker(&#x27;refresh&#x27;);
        }catch(e){}
      }
      return String(el.value)===String(opt.value) || !!opt.selected;
    }catch(e){
      console.warn(&#x27;[TritoX TM] Star field fill failed:&#x27;,e);
      return false;
    }
  }

  function fillAltaMetadataFields(d){
    const results={company:false,date:false,star:false};
    if(d.current_company){
      results.company=setAnyControl(findControlByLabel(&#x27;Current Company&#x27;),d.current_company);
    }
    if(d.auto_renewal_date){
      results.date=setAnyControl(findControlByLabel(&#x27;Auto renewal date&#x27;),d.auto_renewal_date);
    }
    if(d.star_rating){
      results.star=setStarControl(d.star_rating);
      // A short second pass handles AgencyZoom fields that finish rendering
      // just after the rest of Main is available.
      if(!results.star){
        setTimeout(function(){
          const ok=setStarControl(d.star_rating);
          console.log(&#x27;[TritoX TM] Star retry:&#x27;,ok,d.star_rating);
        },250);
      }
    }
    console.log(&#x27;[TritoX TM] ALTA metadata autofill:&#x27;,results,d.current_company,d.auto_renewal_date,d.star_rating);
    return results;
  }

  function fillFields(d){
    fillText(&#x27;cf30203&#x27;, d.vehicles_policy);
    fillSelect(&#x27;cf56698&#x27;, d.bodily_injury);
    fillText(&#x27;cf47028&#x27;, d.home_coverage_a);
    fillText(&#x27;cf37981&#x27;, d.home_annual);
    fillText(&#x27;cf56654&#x27;, d.auto1);
    fillSelect(&#x27;cf56655&#x27;, d.auto1_ded);
    fillText(&#x27;cf56656&#x27;, d.auto2);
    fillSelect(&#x27;cf56657&#x27;, d.auto2_ded);
    fillText(&#x27;cf56692&#x27;, d.auto3);
    fillSelect(&#x27;cf56693&#x27;, d.auto3_ded);
    fillText(&#x27;cf56694&#x27;, d.auto4);
    fillSelect(&#x27;cf56695&#x27;, d.auto4_ded);
    fillText(&#x27;cf56696&#x27;, d.auto5);
    fillSelect(&#x27;cf56697&#x27;, d.auto5_ded);
    fillAltaMetadataFields(d);
    function fillMoneyFields(attempt){
      const mEl = document.querySelector(&#x27;input[name=&quot;customFields[cf30197]&quot;]&#x27;);
      const sEl = document.querySelector(&#x27;input[name=&quot;customFields[cf30199]&quot;]&#x27;);
      if(mEl){
        const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,&#x27;value&#x27;).set;
        setter.call(mEl, String(d.monthly_auto).replace(/[$,]/g,&#x27;&#x27;));
        mEl.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
        mEl.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
      }
      if(sEl){
        const setter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,&#x27;value&#x27;).set;
        setter.call(sEl, String(d.auto_6months).replace(/[$,]/g,&#x27;&#x27;));
        sEl.dispatchEvent(new Event(&#x27;input&#x27;,{bubbles:true}));
        sEl.dispatchEvent(new Event(&#x27;change&#x27;,{bubbles:true}));
      }
      if((!mEl || !sEl) &amp;&amp; attempt &lt; 30){
        setTimeout(function(){ fillMoneyFields(attempt+1); }, 800);
      }
    }
    fillMoneyFields(1);
  }

  let filledTs = 0; // timestamp of last fill action
  let lastSeenDataTs = 0;

  function refreshFillButton(){
    const gmRaw = GM_getValue(&#x27;tritox_az_data&#x27;,&#x27;&#x27;);
    if(!gmRaw) return;
    let gmData;
    try{ gmData = JSON.parse(gmRaw); }catch(e){ return; }
    const gmTs = Number(gmData &amp;&amp; gmData._ts || 0);
    if(!gmTs || gmTs &lt;= filledTs || gmTs &lt;= expiredTs) return;

    // New data must replace any old Fill button even when the AgencyZoom URL
    // and lead panel do not rerender.
    if(gmTs !== lastSeenDataTs){
      lastSeenDataTs = gmTs;
      const existing=document.getElementById(&#x27;tritox-fill-btn&#x27;);
      if(existing) existing.remove();
      const oldOverlay=document.getElementById(&#x27;tritox-fill-overlay&#x27;);
      if(oldOverlay) oldOverlay.remove();
    }
    addFillButton();
  }

  // Fast poll so the popup appears almost immediately after QC finishes.
  setInterval(refreshFillButton, 250);
  setTimeout(refreshFillButton, 100);

  // AgencyZoom is a SPA; rerenders can remove fixed DOM nodes. Re-add the
  // current Fill button after DOM changes without waiting for a navigation.
  const tmObserver=new MutationObserver(function(){
    if(!document.getElementById(&#x27;tritox-fill-btn&#x27;)) refreshFillButton();
  });
  tmObserver.observe(document.documentElement,{childList:true,subtree:true});

})();
</pre>
  </div>
</div>
</body>
</html>
