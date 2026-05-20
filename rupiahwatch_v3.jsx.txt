import { useState, useEffect, useRef } from "react";

const C = {
  bg:"#070709", s1:"#0d0d12", s2:"#111116",
  border:"#1c1c26", border2:"#242432",
  y:"#f0e040", red:"#ff3b3b", blue:"#3b9eff",
  green:"#2dff7e", orange:"#ff8c3b",
  text:"#ebebeb", muted:"#5a5a72", muted2:"#8888a0"
};

const css = `
  @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Schibsted+Grotesk:wght@400;500;600;700;900&family=JetBrains+Mono:wght@400;500;700&display=swap');
  *{margin:0;padding:0;box-sizing:border-box}
  html{scroll-behavior:smooth}
  body{background:#070709;color:#ebebeb;font-family:'Schibsted Grotesk',sans-serif;overflow-x:hidden}
  ::-webkit-scrollbar{width:4px} ::-webkit-scrollbar-track{background:#0d0d12} ::-webkit-scrollbar-thumb{background:#f0e040}
  @keyframes ticker{from{transform:translateX(0)}to{transform:translateX(-50%)}}
  @keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.3;transform:scale(.7)}}
  @keyframes fadeUp{from{opacity:0;transform:translateY(24px)}to{opacity:1;transform:translateY(0)}}
  @keyframes scanline{0%{top:-2px}100%{top:100%}}
  .mono{font-family:'JetBrains Mono',monospace}
  .bebas{font-family:'Bebas Neue',sans-serif}
  .ticker-inner{display:inline-block;animation:ticker 40s linear infinite;white-space:nowrap}
  .pulse{animation:pulse 1.4s ease-in-out infinite}
  .fade-up{animation:fadeUp .7s ease forwards}
`;

// ── tiny primitives ─────────────────────────────────
const Tag = ({c=C.y,children})=>(
  <span className="mono" style={{fontSize:".6rem",color:c,border:`1px solid ${c}44`,padding:".2rem .55rem",textTransform:"uppercase",letterSpacing:".08em"}}>{children}</span>
);
const Label = ({children})=>(
  <div className="mono" style={{fontSize:".66rem",color:C.y,textTransform:"uppercase",letterSpacing:".2em",marginBottom:".9rem",display:"flex",alignItems:"center",gap:".5rem"}}>
    <span style={{opacity:.5}}>//</span>{children}
  </div>
);
const H2 = ({children,style={}})=>(
  <h2 className="bebas" style={{fontSize:"clamp(2.2rem,5vw,3.8rem)",letterSpacing:".03em",lineHeight:1,marginBottom:"1rem",...style}}>{children}</h2>
);
const Sub = ({children})=>(
  <p style={{fontSize:".93rem",color:C.muted2,lineHeight:1.75,maxWidth:560,marginBottom:"2rem"}}>{children}</p>
);
const HR = ()=><div style={{height:1,background:C.border,margin:"1.5rem 0"}}/>;
const Sec = ({id,children,style={}})=>(
  <section id={id} style={{maxWidth:1280,margin:"0 auto",padding:"5rem 2rem",...style}}>{children}</section>
);
const Card = ({children,style={}})=>(
  <div style={{background:C.s1,border:`1px solid ${C.border}`,padding:"1.6rem",...style}}>{children}</div>
);
const Mono = ({children,color=C.muted2,size=".75rem"})=>(
  <span className="mono" style={{fontSize:size,color}}>{children}</span>
);

// ── flag badge ───────────────────────────────────────
const Flag = ({type})=>{
  const map={absurd:[C.red,"Absurd"],high:[C.orange,"High"],medium:[C.blue,"Medium"],ok:[C.green,"OK"],impor:[C.orange,"Impor"],comply:[C.green,"Comply"],partial:[C.y,"Parsial"],violate:[C.red,"Tidak Comply"]};
  const [c,l]=map[type]||[C.muted,"—"];
  return <span className="mono" style={{fontSize:".6rem",color:c,border:`1px solid ${c}44`,background:`${c}10`,padding:".2rem .55rem",textTransform:"uppercase",letterSpacing:".06em",whiteSpace:"nowrap"}}>{l}</span>;
};

// ── progress bar ─────────────────────────────────────
const Bar = ({val,max=100,color=C.y,label,sub})=>(
  <div style={{marginBottom:".8rem"}}>
    {label&&<div style={{display:"flex",justifyContent:"space-between",fontSize:".8rem",marginBottom:".3rem"}}>
      <span>{label}</span><Mono>{val}{typeof val==="number"?"%":""} {sub&&`/ ${sub}`}</Mono>
    </div>}
    <div style={{height:6,background:C.border}}>
      <div style={{width:`${Math.min((typeof val==="number"?val:0)/max*100,100)}%`,height:"100%",background:color,transition:"width 1s ease"}}/>
    </div>
  </div>
);

// ── stat card ────────────────────────────────────────
const Stat = ({num,label,src,color=C.y})=>(
  <div style={{background:C.bg,padding:"1.6rem",borderRight:`1px solid ${C.border}`}}>
    <div className="bebas" style={{fontSize:"2.2rem",color,lineHeight:1,marginBottom:".3rem"}}>{num}</div>
    <div className="mono" style={{fontSize:".62rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>{label}</div>
    {src&&<div style={{fontSize:".68rem",color:C.muted,marginTop:".3rem"}}>{src}</div>}
  </div>
);

// ══════════════════════════════════════════════════════
// NAV
// ══════════════════════════════════════════════════════
const NAV_LINKS = ["17+8","Program","Fitur","Impor","Pemekaran","Lacak","P3DN"];
function Nav(){
  const [scrolled,setScrolled]=useState(false);
  useEffect(()=>{
    const h=()=>setScrolled(window.scrollY>50);
    window.addEventListener("scroll",h);return()=>window.removeEventListener("scroll",h);
  },[]);
  return(
    <nav style={{position:"fixed",top:0,left:0,right:0,zIndex:100,display:"flex",alignItems:"center",justifyContent:"space-between",padding:"1rem 2rem",borderBottom:`1px solid ${scrolled?C.border2:C.border}`,background:"rgba(7,7,9,.9)",backdropFilter:"blur(16px)",transition:"border-color .3s"}}>
      <div className="bebas" style={{fontSize:"1.5rem",letterSpacing:".08em"}}>Rupiah<span style={{color:C.y}}>Watch</span></div>
      <div style={{display:"flex",gap:"1.8rem"}}>
        {NAV_LINKS.map(l=>(
          <a key={l} href={`#${l.toLowerCase()}`} className="mono" style={{fontSize:".66rem",color:C.muted2,textDecoration:"none",textTransform:"uppercase",letterSpacing:".12em",transition:"color .2s"}}
            onMouseEnter={e=>e.target.style.color=C.y} onMouseLeave={e=>e.target.style.color=C.muted2}>{l}</a>
        ))}
      </div>
      <button className="mono" style={{fontSize:".7rem",background:"transparent",border:`1px solid ${C.y}`,color:C.y,padding:".45rem 1.1rem",letterSpacing:".1em",textTransform:"uppercase",cursor:"pointer",transition:"background .2s,color .2s"}}
        onMouseEnter={e=>{e.target.style.background=C.y;e.target.style.color="#000"}}
        onMouseLeave={e=>{e.target.style.background="transparent";e.target.style.color=C.y}}>
        Dashboard
      </button>
    </nav>
  );
}

// ══════════════════════════════════════════════════════
// HERO
// ══════════════════════════════════════════════════════
function Hero(){
  const ROWS=[
    {name:"Pengadaan Server BSSN",inst:"BSSN · TKDN 8%",val:"Rp 31,7M",flag:"impor"},
    {name:"Alat Kesehatan Diagnostik",inst:"Kemenkes · 2025",val:"Rp 48,3M",flag:"absurd"},
    {name:"AC Ruang Pimpinan",inst:"Setda Kab. Morowali",val:"Rp 2,1M",flag:"absurd"},
    {name:"Renovasi Jalan Trans Kalimantan",inst:"Kemen PU · TKDN 71%",val:"Rp 284M",flag:"ok"},
    {name:"Bahan Baku Susu MBG",inst:"BGN · 2025",val:"Rp 12,4M",flag:"high"},
  ];
  return(
    <section style={{minHeight:"100vh",display:"grid",gridTemplateColumns:"1fr 1fr",alignItems:"center",padding:"7rem 2rem 4rem",gap:"3rem",position:"relative",overflow:"hidden"}}>
      <div style={{position:"absolute",inset:0,backgroundImage:`linear-gradient(${C.border} 1px,transparent 1px),linear-gradient(90deg,${C.border} 1px,transparent 1px)`,backgroundSize:"80px 80px",opacity:.3,maskImage:"radial-gradient(ellipse 100% 100% at 50% 50%,black 30%,transparent 80%)"}}/>
      <div style={{position:"absolute",inset:0,background:`radial-gradient(ellipse 60% 50% at 65% 50%,${C.y}06,transparent 60%)`}}/>

      <div style={{position:"relative",zIndex:2}}>
        <div className="mono fade-up" style={{fontSize:".67rem",color:C.y,textTransform:"uppercase",letterSpacing:".2em",marginBottom:"1.4rem",display:"flex",alignItems:"center",gap:".7rem",animationDelay:".1s",opacity:0}}>
          <span style={{width:28,height:1,background:C.y,display:"inline-block"}}/>Open Source · Data Resmi · Untuk Rakyat
        </div>
        <h1 className="bebas fade-up" style={{fontSize:"clamp(4rem,9vw,8rem)",lineHeight:.92,letterSpacing:".02em",animationDelay:".25s",opacity:0}}>
          AWASI<br/><span style={{color:C.y}}>UANG</span><br/><span style={{color:C.red}}>RAKYAT.</span>
        </h1>
        <p className="fade-up" style={{marginTop:"1.8rem",fontSize:"1rem",color:C.muted2,lineHeight:1.75,maxWidth:480,animationDelay:".4s",opacity:0}}>
          Dari <strong style={{color:C.text}}>rencana pengadaan</strong> sampai <strong style={{color:C.text}}>pemenang tender</strong>, dari <strong style={{color:C.text}}>realisasi anggaran</strong> sampai <strong style={{color:C.text}}>ketimpangan antar pulau</strong> — semua bisa dilacak.
        </p>
        <div className="fade-up" style={{marginTop:"2rem",display:"flex",gap:"1rem",animationDelay:".55s",opacity:0}}>
          <button style={{fontFamily:"'Schibsted Grotesk',sans-serif",fontWeight:700,fontSize:".88rem",background:C.y,color:"#000",padding:".8rem 2rem",border:"none",cursor:"pointer"}}>Mulai Telusuri →</button>
          <button className="mono" style={{fontSize:".72rem",background:"none",border:`1px solid ${C.border2}`,color:C.muted2,padding:".8rem 1.6rem",cursor:"pointer",letterSpacing:".08em",textTransform:"uppercase"}}>GitHub</button>
        </div>
      </div>

      <div className="fade-up" style={{position:"relative",zIndex:2,animationDelay:".7s",opacity:0}}>
        <Card style={{padding:0,overflow:"hidden"}}>
          <div className="mono" style={{padding:".7rem 1.2rem",borderBottom:`1px solid ${C.border}`,display:"flex",justifyContent:"space-between",alignItems:"center",fontSize:".67rem",color:C.muted2,textTransform:"uppercase",letterSpacing:".12em"}}>
            <span>Feed Anomali</span>
            <span style={{display:"flex",alignItems:"center",gap:".4rem",color:C.red}}>
              <span className="pulse" style={{width:6,height:6,borderRadius:"50%",background:C.red,display:"inline-block"}}/>Live
            </span>
          </div>
          {ROWS.map((r,i)=>(
            <div key={i} style={{display:"grid",gridTemplateColumns:"1fr auto auto",gap:".8rem",padding:".85rem 1.2rem",borderTop:i?`1px solid ${C.border}`:"none",alignItems:"center",transition:"background .15s"}}
              onMouseEnter={e=>e.currentTarget.style.background=C.s2}
              onMouseLeave={e=>e.currentTarget.style.background="transparent"}>
              <div>
                <div style={{fontSize:".82rem",fontWeight:500}}>{r.name}</div>
                <Mono size=".68rem" color={C.muted}>{r.inst}</Mono>
              </div>
              <Mono>{r.val}</Mono>
              <Flag type={r.flag}/>
            </div>
          ))}
          <div className="mono" style={{padding:".65rem 1.2rem",borderTop:`1px solid ${C.border}`,fontSize:".62rem",color:C.muted,display:"flex",justifyContent:"space-between"}}>
            <span>SIRUP LKPP + TKDN</span><span>{new Date().toLocaleDateString("id-ID")}</span>
          </div>
        </Card>
      </div>
    </section>
  );
}

// ══════════════════════════════════════════════════════
// TICKER
// ══════════════════════════════════════════════════════
const TICKS=["SIRUP LKPP","LPSE","DJPb Kemenkeu","BPK RI","AHU Kemenkumham","LHKPN KPK","TKDN Kemenperin","BPS Exim","INSW","SIPD Kemendagri","DJPK Transfer Daerah","Data.go.id"];
function Ticker(){
  const items=[...TICKS,...TICKS];
  return(
    <div style={{background:C.y,color:"#000",padding:".55rem 0",overflow:"hidden",whiteSpace:"nowrap"}}>
      <div className="ticker-inner mono" style={{fontSize:".67rem",fontWeight:500,letterSpacing:".1em",textTransform:"uppercase"}}>
        {items.map((t,i)=><span key={i} style={{padding:"0 2.5rem"}}>▸ {t}</span>)}
      </div>
    </div>
  );
}

// ══════════════════════════════════════════════════════
// STATS
// ══════════════════════════════════════════════════════
function Stats(){
  const data=[
    {num:"US$ 729Jt",label:"Impor Alkes 2024",src:"BPS HS Code 9018"},
    {num:"52%",label:"Alkes masih impor",src:"Kemenperin 2024"},
    {num:"86%",label:"Izin Edar = Impor",src:"FEB UI Research"},
    {num:"83.363",label:"Koperasi Desa Terdaftar",src:"SIMKOP Apr 2026"},
  ];
  return(
    <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",borderBottom:`1px solid ${C.border}`}}>
      {data.map((d,i)=><Stat key={i} {...d}/>)}
    </div>
  );
}

// ══════════════════════════════════════════════════════
// 17+8 HEADLINE TRACKER
// ══════════════════════════════════════════════════════
const TUNTUTAN_17=[
  {pihak:"Presiden",tuntutan:"Tarik TNI dari pengamanan sipil",status:"partial",update:"Prabowo larang anggota DPR ke luar negeri & cabut tunjangan · 31 Agu 2025"},
  {pihak:"Presiden",tuntutan:"Bentuk Tim Investigasi Independen kasus Affan Kurniawan & Umar Amarudin",status:"partial",update:"Proses hukum transparan dijanjikan · belum ada hasil resmi"},
  {pihak:"DPR",tuntutan:"Bekukan kenaikan gaji & batalkan fasilitas baru termasuk pensiun",status:"comply",update:"DPR umumkan 6 keputusan · tunjangan perumahan dihentikan 31 Agu 2025"},
  {pihak:"DPR",tuntutan:"Publikasikan transparansi anggaran DPR secara proaktif",status:"partial",update:"Komitmen diumumkan · implementasi penuh belum terverifikasi"},
  {pihak:"DPR",tuntutan:"Dorong Badan Kehormatan DPR periksa anggota bermasalah via KPK",status:"partial",update:"MKD berkomitmen tindak anggota bermasalah · progres belum publik"},
  {pihak:"Polisi",tuntutan:"Bebaskan seluruh demonstran yang ditahan",status:"partial",update:"Sebagian dibebaskan · data lengkap belum terkonfirmasi"},
  {pihak:"Polisi",tuntutan:"Hentikan kekerasan polisi & taati SOP pengendalian massa",status:"partial",update:"Pernyataan komitmen ada · insiden lanjutan belum terpantau"},
  {pihak:"TNI",tuntutan:"Tangkap dan proses anggota yang lakukan kekerasan & langgar HAM",status:"partial",update:"Proses hukum dijanjikan transparan · perkembangan belum publik"},
  {pihak:"Ekonomi",tuntutan:"Ambil langkah darurat cegah PHK massal & lindungi buruh kontrak",status:"open",update:"Belum ada kebijakan darurat spesifik yang diumumkan"},
  {pihak:"Ekonomi",tuntutan:"Buka dialog dengan serikat buruh soal upah minimum & outsourcing",status:"open",update:"Belum ada jadwal dialog resmi yang dikonfirmasi"},
];

const TUNTUTAN_8=[
  {no:1,tuntutan:"Bersihkan & reformasi DPR besar-besaran — audit independen publik",deadline:"31 Agu 2026",status:"partial",update:"Komitmen ada dari DPR · audit independen belum dimulai"},
  {no:2,tuntutan:"Reformasi partai politik & kuatkan pengawasan eksekutif — laporan keuangan partai",deadline:"31 Agu 2026",status:"open",update:"Belum ada regulasi baru yang disahkan"},
  {no:3,tuntutan:"Susun rencana reformasi perpajakan yang lebih adil",deadline:"31 Agu 2026",status:"partial",update:"Menkeu baru Purbaya Yudhi Sadewa (reshuf 8 Sep 2025) · kajian berlangsung"},
  {no:4,tuntutan:"Sahkan & tegakkan UU Perampasan Aset Koruptor",deadline:"31 Agu 2026",status:"open",update:"RUU masih di DPR · belum disahkan per Mei 2026"},
  {no:5,tuntutan:"Reformasi kepemimpinan & sistem kepolisian — profesional & humanis",deadline:"31 Agu 2026",status:"open",update:"Belum ada reformasi struktural yang diumumkan"},
  {no:6,tuntutan:"TNI kembali ke barak tanpa pengecualian",deadline:"31 Agu 2026",status:"open",update:"TNI di jabatan sipil masih berlanjut · belum ada penarikan resmi"},
  {no:7,tuntutan:"Perkuat Komnas HAM & lembaga pengawas independen",deadline:"31 Agu 2026",status:"open",update:"Belum ada penguatan anggaran atau mandat baru Komnas HAM"},
  {no:8,tuntutan:"Tinjau ulang PSN, UU Cipta Kerja & tata kelola Danantara",deadline:"31 Agu 2026",status:"partial",update:"Pembahasan berlangsung · belum ada revisi resmi yang disahkan"},
];

const STATUS_MAP={
  comply:[C.green,"✓ Terpenuhi"],
  partial:[C.y,"~ Sebagian"],
  open:[C.red,"✗ Belum"],
};

function Tracker17(){
  const [tab,setTab]=useState(0);
  const comply17=TUNTUTAN_17.filter(t=>t.status==="comply").length;
  const partial17=TUNTUTAN_17.filter(t=>t.status==="partial").length;
  const comply8=TUNTUTAN_8.filter(t=>t.status==="comply").length;
  const partial8=TUNTUTAN_8.filter(t=>t.status==="partial").length;

  return(
    <div style={{background:C.bg,borderTop:`2px solid ${C.y}`,borderBottom:`1px solid ${C.border}`}}>
      <Sec>
        {/* HEADLINE */}
        <div style={{display:"flex",alignItems:"flex-start",justifyContent:"space-between",flexWrap:"wrap",gap:"1rem",marginBottom:"2rem"}}>
          <div>
            <div style={{display:"flex",alignItems:"center",gap:".8rem",marginBottom:".8rem"}}>
              <div className="bebas" style={{fontSize:"clamp(2rem,5vw,3.5rem)",letterSpacing:".03em",lineHeight:1}}>
                TRACKER <span style={{color:C.y}}>17+8</span> TUNTUTAN RAKYAT
              </div>
              <span className="mono" style={{fontSize:".65rem",color:C.red,border:`1px solid ${C.red}44`,padding:".25rem .6rem",display:"flex",alignItems:"center",gap:".4rem"}}>
                <span className="pulse" style={{width:5,height:5,borderRadius:"50%",background:C.red,display:"inline-block"}}/>DEADLINE: 31 AGU 2026
              </span>
            </div>
            <p style={{fontSize:".9rem",color:C.muted2,lineHeight:1.7,maxWidth:640}}>
              Dirumuskan dari aspirasi 211 organisasi masyarakat sipil — 17 tuntutan mendesak (deadline 5 Sep 2025) + 8 reformasi struktural (deadline 31 Agu 2026). RupiahWatch pantau progresnya berbasis data publik.
            </p>
          </div>
          {/* SCOREBOARD */}
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:1,background:C.border,border:`1px solid ${C.border}`,minWidth:280,flexShrink:0}}>
            {[
              [`${comply17}/17`,`Tuntutan 17 Terpenuhi`,C.green],
              [`${partial17}/17`,`Tuntutan 17 Sebagian`,C.y],
              [`${comply8}/8`,`Tuntutan 8 Terpenuhi`,C.green],
              [`${partial8}/8`,`Tuntutan 8 Sebagian`,C.y],
            ].map(([n,l,c],i)=>(
              <div key={i} style={{background:C.s1,padding:"1rem 1.2rem"}}>
                <div className="bebas" style={{fontSize:"1.8rem",color:c,lineHeight:1,marginBottom:".2rem"}}>{n}</div>
                <Mono color={C.muted} size=".6rem">{l.toUpperCase()}</Mono>
              </div>
            ))}
          </div>
        </div>

        {/* KONTEKS */}
        <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:1,background:C.border,border:`1px solid ${C.border}`,marginBottom:"1.5rem"}}>
          {[
            ["25-30 Agu 2025","Demonstrasi besar di 32 provinsi, 107 titik aksi nasional",C.muted2],
            ["28 Agu 2025","Affan Kurniawan (ojol) tewas dilindas Rantis Brimob — pemicu eskalasi",C.red],
            ["31 Agu 2025","Prabowo larang DPR ke luar negeri & cabut tunjangan sebagai respons awal",C.y],
            ["8 Sep 2025","Reshuffle kabinet — Sri Mulyani diganti Purbaya Yudhi Sadewa",C.blue],
          ].map(([tgl,ket,c],i)=>(
            <div key={i} style={{background:C.s1,padding:"1.2rem"}}>
              <Mono color={c} size=".65rem">{tgl.toUpperCase()}</Mono>
              <p style={{fontSize:".78rem",color:C.muted2,lineHeight:1.55,marginTop:".4rem"}}>{ket}</p>
            </div>
          ))}
        </div>

        {/* TABS */}
        <div style={{display:"flex",border:`1px solid ${C.border}`,overflow:"hidden",marginBottom:1}}>
          {[`17 Tuntutan Mendesak (5 Sep 2025)`,`8 Tuntutan Struktural (31 Agu 2026)`].map((t,i)=>(
            <button key={i} className="mono" onClick={()=>setTab(i)} style={{flex:1,padding:".8rem",fontSize:".68rem",background:tab===i?C.y:C.s2,color:tab===i?"#000":C.muted2,border:"none",borderLeft:i?`1px solid ${C.border}`:"none",cursor:"pointer",textTransform:"uppercase",letterSpacing:".08em",fontWeight:tab===i?700:400,transition:"all .2s"}}>{t}</button>
          ))}
        </div>

        {/* 17 TUNTUTAN */}
        {tab===0&&(
          <div style={{border:`1px solid ${C.border}`}}>
            <div className="mono" style={{display:"grid",gridTemplateColumns:"70px 1fr 1fr 100px",background:C.border,padding:".65rem 1.2rem",gap:"1rem",fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>
              {["Pihak","Tuntutan","Update Terkini","Status"].map(h=><span key={h}>{h}</span>)}
            </div>
            {TUNTUTAN_17.map((t,i)=>{
              const [sc,sl]=STATUS_MAP[t.status];
              return(
                <div key={i} style={{display:"grid",gridTemplateColumns:"70px 1fr 1fr 100px",padding:".9rem 1.2rem",gap:"1rem",borderTop:`1px solid ${C.border}`,alignItems:"start",background:i%2?C.s1:"transparent",fontSize:".82rem"}}>
                  <Mono color={C.blue} size=".65rem">{t.pihak.toUpperCase()}</Mono>
                  <span style={{fontWeight:500,lineHeight:1.45}}>{t.tuntutan}</span>
                  <span style={{fontSize:".75rem",color:C.muted2,lineHeight:1.5}}>{t.update}</span>
                  <span className="mono" style={{fontSize:".65rem",color:sc,fontWeight:700}}>{sl}</span>
                </div>
              );
            })}
            <div className="mono" style={{padding:".65rem 1.2rem",borderTop:`1px solid ${C.border}`,fontSize:".62rem",color:C.muted,display:"flex",justifyContent:"space-between"}}>
              <span>{comply17} terpenuhi · {partial17} sebagian · {TUNTUTAN_17.length-comply17-partial17} belum · dari 17 tuntutan</span>
              <span>Sumber: Detik · CNBC · Liputan6 · Sep 2025</span>
            </div>
          </div>
        )}

        {/* 8 TUNTUTAN */}
        {tab===1&&(
          <div style={{border:`1px solid ${C.border}`}}>
            <div className="mono" style={{display:"grid",gridTemplateColumns:"2rem 1fr 1fr 100px",background:C.border,padding:".65rem 1.2rem",gap:"1rem",fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>
              {["#","Tuntutan Struktural","Update Terkini","Status"].map(h=><span key={h}>{h}</span>)}
            </div>
            {TUNTUTAN_8.map((t,i)=>{
              const [sc,sl]=STATUS_MAP[t.status];
              return(
                <div key={i} style={{display:"grid",gridTemplateColumns:"2rem 1fr 1fr 100px",padding:".9rem 1.2rem",gap:"1rem",borderTop:`1px solid ${C.border}`,alignItems:"start",background:i%2?C.s1:"transparent",fontSize:".82rem"}}>
                  <Mono color={C.muted}>{t.no}</Mono>
                  <div>
                    <span style={{fontWeight:500,lineHeight:1.45}}>{t.tuntutan}</span>
                    <div style={{marginTop:".3rem"}}><Tag c={C.muted2}>{t.deadline}</Tag></div>
                  </div>
                  <span style={{fontSize:".75rem",color:C.muted2,lineHeight:1.5}}>{t.update}</span>
                  <span className="mono" style={{fontSize:".65rem",color:sc,fontWeight:700}}>{sl}</span>
                </div>
              );
            })}
            <div className="mono" style={{padding:".65rem 1.2rem",borderTop:`1px solid ${C.border}`,fontSize:".62rem",color:C.muted,display:"flex",justifyContent:"space-between"}}>
              <span>{comply8} terpenuhi · {partial8} sebagian · {TUNTUTAN_8.length-comply8-partial8} belum · dari 8 tuntutan · deadline 31 Agu 2026</span>
              <span>Data: Detik · CNBC · Binokular · Liputan6 · 2025</span>
            </div>
          </div>
        )}

        <div className="mono" style={{marginTop:".8rem",fontSize:".65rem",color:C.muted,padding:".7rem",border:`1px solid ${C.border}`,background:`${C.y}04`}}>
          ⚠ STATUS INDEPENDEN: Penilaian berdasarkan pernyataan resmi pemerintah yang terpublikasi — bukan klaim sepihak. "Sebagian" = ada respons tapi belum implementasi penuh. Update berkala berdasarkan sumber media terverifikasi.
        </div>
      </Sec>
    </div>
  );
}

// ══════════════════════════════════════════════════════
// PROGRAM TRACKER
// ══════════════════════════════════════════════════════
const TABS=["MBG & SPPG","Koperasi Desa","Hilirisasi","Program Lain"];

function MBGTab(){
  return(
    <div>
      <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:1,background:C.border,marginBottom:1}}>
        {[["56,1 Juta","Penerima Manfaat","Kemenkeu Jan 2026"],["27.735","SPPG Aktif","BGN Apr 2026"],["Rp 70,2T","Realisasi Apr 2026","20,9% dari pagu Rp335T"],["789 Rb","Tenaga Kerja","Wamenkeu Jan 2026"]].map(([n,l,s],i)=>(
          <div key={i} style={{background:C.bg,padding:"1.4rem"}}>
            <div className="bebas" style={{fontSize:"1.8rem",color:C.y,lineHeight:1,marginBottom:".3rem"}}>{n}</div>
            <div className="mono" style={{fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>{l}</div>
            <div style={{fontSize:".7rem",color:C.muted,marginTop:".3rem"}}>{s}</div>
          </div>
        ))}
      </div>
      <Card>
        <Mono color={C.muted}>Progress vs Target</Mono>
        <div style={{marginTop:"1rem"}}>
          <Bar val={67.7} label="Penerima Manfaat" sub="82,9 Jt target" color={C.y}/>
          <Bar val={92.5} label="SPPG Aktif" sub="30.000 target" color={C.green}/>
          <Bar val={20.9} label="Realisasi Anggaran 2026" sub="Rp335T pagu" color={C.blue}/>
        </div>
      </Card>
      <div className="mono" style={{marginTop:".8rem",fontSize:".65rem",color:C.muted,padding:".7rem",border:`1px solid ${C.border}`,background:`${C.y}05`}}>
        ⚠ CATATAN INDEPENDEN: Realisasi 2025 = Rp51,5T dari pagu Rp71T (72,5%). Data TKDN bahan baku belum dipublikasi BGN.
      </div>
    </div>
  );
}

function KoperasiTab(){
  return(
    <div>
      <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:1,background:C.border,marginBottom:1}}>
        {[["83.363","Berbadan Hukum","SIMKOP Apr 2026"],["1.061","Operasional Aktif","KSP Mei 2026"],["9.000+","Gedung Siap Fisik","KSP Mei 2026"],["697 Rb","Pengurus Terdaftar","SIMKOP Apr 2026"]].map(([n,l,s],i)=>(
          <div key={i} style={{background:C.bg,padding:"1.4rem"}}>
            <div className="bebas" style={{fontSize:"1.8rem",color:C.y,lineHeight:1,marginBottom:".3rem"}}>{n}</div>
            <div className="mono" style={{fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>{l}</div>
            <div style={{fontSize:".7rem",color:C.muted,marginTop:".3rem"}}>{s}</div>
          </div>
        ))}
      </div>
      <Card>
        <Bar val={100} label="Berbadan Hukum" sub="80.000 target ✓ Melampaui" color={C.green}/>
        <Bar val={1.3} label="Beroperasi Aktif" sub="80.000 target" color={C.orange}/>
      </Card>
      <div className="mono" style={{marginTop:".8rem",fontSize:".65rem",color:C.muted,padding:".7rem",border:`1px solid ${C.border}`,background:`${C.y}05`}}>
        ⚠ CATATAN INDEPENDEN: Legalitas melampaui target, tapi hanya 1,3% beroperasi aktif. Gap antara dokumen dan realita lapangan perlu dipantau.
      </div>
    </div>
  );
}

function HilirisasiTab(){
  const proyek=[
    ["1-2","Kilang Gasoline Dumai & Cilacap","Riau · Jateng","Energi","ok"],
    ["3-5","Tangki BBM (Palaran, Biak, Maumere)","Kaltim · Papua · NTT","Energi","ok"],
    ["6","Produksi DME 1,4 Jt Ton/Tahun","Tanjung Enim, Sumsel","Energi","ok"],
    ["7","Manufaktur Baja Nirkarat dari Nikel","IMIP Morowali, Sulteng","Mineral","medium"],
    ["8","Produksi Slab Baja dari Bijih Besi Lokal","Cilegon, Banten","Mineral","medium"],
    ["9","Ekosistem Aspal Buton","Karawang, Jabar","Mineral","medium"],
    ["10-13","Proyek Agroindustri Perkebunan","Berbagai daerah","Pertanian","ok"],
  ];
  return(
    <div>
      <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:1,background:C.border,marginBottom:1}}>
        {[["13 Proyek","Fase II Groundbreaking","Apr 2026"],["Rp 116T","Nilai Investasi","Presiden RI Apr 2026"],["600 Rb","Proyeksi Naker","Dari 13 proyek"],["3 Sektor","Energi·Mineral·Pertanian","Sumber: Kompas"]].map(([n,l,s],i)=>(
          <div key={i} style={{background:C.bg,padding:"1.4rem"}}>
            <div className="bebas" style={{fontSize:"1.8rem",color:C.y,lineHeight:1,marginBottom:".3rem"}}>{n}</div>
            <div className="mono" style={{fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>{l}</div>
            <div style={{fontSize:".7rem",color:C.muted,marginTop:".3rem"}}>{s}</div>
          </div>
        ))}
      </div>
      <div style={{border:`1px solid ${C.border}`}}>
        <div className="mono" style={{display:"grid",gridTemplateColumns:"2rem 3fr 2fr 1.2fr 1fr",background:C.border,padding:".65rem 1rem",gap:".8rem",fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>
          {["#","Proyek","Lokasi","Sektor","—"].map((h,i)=><span key={i}>{h}</span>)}
        </div>
        {proyek.map(([no,nama,lokasi,sektor,flag],i)=>(
          <div key={i} style={{display:"grid",gridTemplateColumns:"2rem 3fr 2fr 1.2fr 1fr",padding:".75rem 1rem",gap:".8rem",borderTop:`1px solid ${C.border}`,alignItems:"center",background:i%2?C.s1:"transparent",fontSize:".8rem"}}>
            <Mono color={C.muted}>{no}</Mono>
            <span style={{fontWeight:600}}>{nama}</span>
            <Mono>{lokasi}</Mono>
            <Mono color={C.blue}>{sektor}</Mono>
            <Flag type={flag}/>
          </div>
        ))}
        <div className="mono" style={{padding:".65rem 1rem",borderTop:`1px solid ${C.border}`,fontSize:".62rem",color:C.muted,display:"flex",justifyContent:"space-between"}}>
          <span>13 proyek · Rp116 triliun total</span><span>Kompas · KSP · 29 Apr 2026</span>
        </div>
      </div>
    </div>
  );
}

function LainnyaTab(){
  const items=[
    {label:"Cek Kesehatan Gratis",num:"Aktif Berjalan",desc:"Berjalan di seluruh provinsi sejak awal 2025. Data agregat nasional belum dipublikasi.",src:"Pemprov Kepri · Agu 2025",color:C.blue},
    {label:"Sekolah Rakyat",num:"76 Ribu Naker",desc:"Menyerap 76 ribu tenaga kerja. Fokus anak putus sekolah dari keluarga miskin ekstrem.",src:"IDNTimes · rekap Apr 2026",color:C.blue},
    {label:"KUR & UMKM",num:"Rp 270T",desc:"Plafon KUR 2025 membuka akses pembiayaan bagi jutaan UMKM di seluruh Indonesia.",src:"Kemenkeu APBN Kita Jan 2026",color:C.blue},
    {label:"Total Lapangan Kerja",num:"4 Juta+",desc:"MBG (2,7Jt) + Koperasi + Sekolah Rakyat + Hilirisasi. Dari target 19 juta (2029).",src:"IDNTimes · rekap resmi",color:C.green},
    {label:"Tingkat Pengangguran",num:"4,74%",desc:"Turun dari sebelumnya. Orang bekerja bertambah 3,3 juta (Nov 2025 vs Agu 2024).",src:"BPS via Kemenkeu Feb 2026",color:C.green},
    {label:"Kemiskinan",num:"8,25%",desc:"Turun dari 9,03% (2024). Masih perlu kerja keras capai target 0% kemiskinan ekstrem.",src:"BPS Feb 2026",color:C.green},
  ];
  return(
    <div style={{display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:1,background:C.border,border:`1px solid ${C.border}`}}>
      {items.map((item,i)=>(
        <div key={i} style={{background:C.bg,padding:"1.6rem"}}>
          <Mono color={item.color} size=".62rem">{item.label.toUpperCase()}</Mono>
          <div className="bebas" style={{fontSize:"1.8rem",color:C.y,lineHeight:1,margin:".4rem 0"}}>{item.num}</div>
          <p style={{fontSize:".8rem",color:C.muted2,lineHeight:1.6,marginBottom:".6rem"}}>{item.desc}</p>
          <Mono color={C.muted} size=".62rem">{item.src}</Mono>
        </div>
      ))}
    </div>
  );
}

const TAB_CONTENT=[MBGTab,KoperasiTab,HilirisasiTab,LainnyaTab];

function ProgramTracker(){
  const [active,setActive]=useState(0);
  const Content=TAB_CONTENT[active];
  return(
    <div id="program" style={{background:C.s1,borderTop:`1px solid ${C.border}`,borderBottom:`1px solid ${C.border}`}}>
      <Sec>
        <Label>Tracker Program Prioritas</Label>
        <H2><span style={{color:C.y}}>CAPAIAN NYATA</span><br/>PROGRAM PRABOWO.</H2>
        <Sub>Data valid dari sumber resmi — bukan hanya kritik, tapi juga pantau capaian positif secara transparan.</Sub>
        <div style={{display:"flex",border:`1px solid ${C.border}`,overflow:"hidden",marginBottom:1}}>
          {TABS.map((t,i)=>(
            <button key={i} className="mono" onClick={()=>setActive(i)} style={{flex:1,padding:".8rem",fontSize:".68rem",background:active===i?C.y:C.s2,color:active===i?"#000":C.muted2,border:"none",borderLeft:i?`1px solid ${C.border}`:"none",cursor:"pointer",textTransform:"uppercase",letterSpacing:".08em",fontWeight:active===i?700:400,transition:"background .2s,color .2s"}}>{t}</button>
          ))}
        </div>
        <Content/>
      </Sec>
    </div>
  );
}

// ══════════════════════════════════════════════════════
// FEATURES
// ══════════════════════════════════════════════════════
const FEATURES=[
  {icon:"🔗",title:"End-to-End Tracker",desc:"SIRUP → LPSE → DJPb → BPK dalam satu pencarian. Pertama di Indonesia.",tag:"Eksklusif"},
  {icon:"🌏",title:"Import Dependency Score",desc:"Rapor A-E per sektor: seberapa tergantung impor. Sambungkan TKDN + BPS real-time.",tag:"Eksklusif"},
  {icon:"💸",title:"Bocor Calculator",desc:"Berapa rupiah belanja pemerintah yang keluar ke luar negeri — breakdown per negara.",tag:"Eksklusif"},
  {icon:"🏢",title:"Vendor Intelligence",desc:"Sambungkan LPSE + AHU + LHKPN. Deteksi konflik kepentingan berbasis data resmi.",tag:"Belum ada di platform lain"},
  {icon:"⚖️",title:"P3DN Compliance Audit",desc:"Otomatis flag pengadaan yang beli impor padahal produk lokal TKDN tersedia.",tag:"Eksklusif"},
  {icon:"🗺️",title:"Peta Ketimpangan Pulau",desc:"Visualisasi anggaran per pulau, per provinsi, per kab/kota. Filter SIRUP/LPSE regional.",tag:"Eksklusif"},
  {icon:"⚖️",title:"Fiskal Justice Map",desc:"Kontribusi pajak vs penerimaan transfer per daerah. Siapa yang subsidinya 'diambil'?",tag:"Eksklusif"},
  {icon:"🔪",title:"Tracker Pemekaran",desc:"Argumen data kelayakan pemekaran + arsip 300+ usulan di DPR. Simulasi APBD mandiri.",tag:"Eksklusif"},
  {icon:"📅",title:"Timeline Proyek Mangkrak",desc:"Deteksi otomatis proyek yang direncanakan tapi tidak pernah selesai — berdasarkan data lintas tahun.",tag:"Eksklusif"},
  {icon:"🌾",title:"Dana Desa Tracker",desc:"74 ribu desa terpantau. Dana cair berapa, sudah dipakai untuk apa, ada temuan BPK?",tag:"Cakupan terluas"},
  {icon:"📣",title:"Whistleblower Mini",desc:"Form anonim untuk laporan warga/ASN. Diteruskan ke KPK atau BPK.",tag:"Partisipatif"},
  {icon:"🔌",title:"API Publik",desc:"Data RupiahWatch bisa diakses developer, jurnalis, dan akademisi. Jadi infrastruktur.",tag:"Open"},
];

function Features(){
  return(
    <Sec id="fitur">
      <Label>Fitur Utama</Label>
      <H2>LEBIH DARI<br/>DETEKSI ANOMALI.</H2>
      <Sub>RupiahWatch sambungkan seluruh rantai anggaran — dari perencanaan sampai kemana uang rakyat benar-benar pergi.</Sub>
      <div style={{display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:1,background:C.border,border:`1px solid ${C.border}`}}>
        {FEATURES.map((f,i)=>(
          <div key={i} style={{background:C.s1,padding:"2rem",position:"relative",overflow:"hidden",transition:"background .2s"}}
            onMouseEnter={e=>{e.currentTarget.style.background=C.s2;e.currentTarget.querySelector(".accent-line").style.transform="scaleX(1)"}}
            onMouseLeave={e=>{e.currentTarget.style.background=C.s1;e.currentTarget.querySelector(".accent-line").style.transform="scaleX(0)"}}>
            <div className="accent-line" style={{position:"absolute",bottom:0,left:0,right:0,height:2,background:C.y,transform:"scaleX(0)",transformOrigin:"left",transition:"transform .35s ease"}}/>
            <div style={{fontSize:"1.5rem",marginBottom:"1rem"}}>{f.icon}</div>
            <div style={{fontWeight:700,fontSize:"1rem",marginBottom:".5rem"}}>{f.title}</div>
            <p style={{fontSize:".83rem",color:C.muted2,lineHeight:1.65,marginBottom:".8rem"}}>{f.desc}</p>
            <Tag>{f.tag}</Tag>
          </div>
        ))}
      </div>
    </Sec>
  );
}

// ══════════════════════════════════════════════════════
// IMPORT DEPENDENCY
// ══════════════════════════════════════════════════════
const RAPOR=[
  {sektor:"Izin Edar Alkes",pct:86,color:C.red,grade:"E"},
  {sektor:"Alat Kesehatan",pct:55.6,color:C.red,grade:"D"},
  {sektor:"e-Katalog Alkes",pct:52,color:C.orange,grade:"D"},
  {sektor:"CT Scan",pct:100,color:C.red,grade:"E"},
  {sektor:"Alkes 2019",pct:92,color:C.red,grade:"E"},
  {sektor:"Alkes 2024",pct:52,color:C.orange,grade:"D"},
];
const GRADE_COLORS={A:C.green,B:"#a8ff78",C:C.y,D:C.orange,E:C.red};

function ImportDep(){
  const [sector,setSector]=useState(0);
  const SECTORS=[
    {name:"🏥 Alat Kesehatan",pct:"52%",total:"US$ 729Jt",cn:"193,69 Jt",us:"89,98 Jt",de:"75,92 Jt",jp:"45,49 Jt",src:"BPS HS 9018 · 2024"},
    {name:"💻 IT & Digital",pct:"~78%",total:"Data validasi",cn:"—",us:"—",de:"—",jp:"—",src:"Data dalam proses validasi"},
    {name:"⚔️ Pertahanan",pct:"~55%",total:"Data validasi",cn:"—",us:"—",de:"—",jp:"—",src:"Data dalam proses validasi"},
    {name:"🌾 Pangan & MBG",pct:"~34%",total:"Data validasi",cn:"—",us:"—",de:"—",jp:"—",src:"Data dalam proses validasi"},
  ];
  const s=SECTORS[sector];
  return(
    <div id="impor" style={{background:C.s1,borderTop:`1px solid ${C.border}`,borderBottom:`1px solid ${C.border}`}}>
      <Sec>
        <Label>Ketergantungan Impor</Label>
        <H2>RAPOR KEMANDIRIAN<br/>EKONOMI NASIONAL.</H2>
        <Sub>Data TKDN Kemenperin vs realisasi pengadaan — seberapa "Indonesia" belanja pemerintah kita.</Sub>
        <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:"3rem",marginTop:"1rem"}}>
          <div>
            <Mono color={C.muted}>Sektor Alat Kesehatan — Sumber Resmi</Mono>
            <div style={{marginTop:"1rem",display:"flex",flexDirection:"column",gap:".5rem"}}>
              {RAPOR.map((r,i)=>(
                <div key={i} style={{display:"grid",gridTemplateColumns:"140px 1fr 48px 48px",gap:"1rem",alignItems:"center",padding:".85rem 1rem",background:C.bg,border:`1px solid ${C.border}`}}>
                  <span style={{fontSize:".82rem",fontWeight:600}}>{r.sektor}</span>
                  <div style={{height:5,background:C.border}}>
                    <div style={{width:`${Math.min(r.pct,100)}%`,height:"100%",background:r.color}}/>
                  </div>
                  <Mono>{r.pct}%</Mono>
                  <div className="bebas" style={{fontSize:"1.2rem",color:GRADE_COLORS[r.grade],textAlign:"center"}}>{r.grade}</div>
                </div>
              ))}
            </div>
          </div>
          <div>
            <div style={{display:"flex",gap:0,marginBottom:1}}>
              {SECTORS.map((sec,i)=>(
                <button key={i} className="mono" onClick={()=>setSector(i)} style={{flex:1,padding:".55rem .4rem",fontSize:".58rem",background:sector===i?C.y:C.s2,color:sector===i?"#000":C.muted2,border:`1px solid ${C.border}`,cursor:"pointer",textAlign:"left",letterSpacing:".04em"}}>
                  {sec.name}
                </button>
              ))}
            </div>
            <Card>
              <div className="mono" style={{fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".12em",marginBottom:".5rem"}}>Bocor ke Luar Negeri</div>
              <div className="bebas" style={{fontSize:"3rem",color:C.red,lineHeight:1,marginBottom:".3rem"}}>{s.total}</div>
              <p style={{fontSize:".82rem",color:C.muted2,lineHeight:1.6,marginBottom:"1rem"}}>{s.pct} produk impor dari sektor ini keluar dari ekonomi Indonesia.</p>
              <Mono color={C.muted} size=".6rem">BREAKDOWN PER NEGARA · {s.src}</Mono>
              {[["🇨🇳 China",s.cn],["🇺🇸 Amerika Serikat",s.us],["🇩🇪 Jerman",s.de],["🇯🇵 Jepang",s.jp]].map(([n,v],i)=>(
                <div key={i} style={{display:"flex",justifyContent:"space-between",padding:".5rem 0",borderBottom:`1px solid ${C.border}`,fontSize:".82rem"}}>
                  <span style={{color:C.muted2}}>{n}</span><Mono>US$ {v}</Mono>
                </div>
              ))}
            </Card>
          </div>
        </div>
      </Sec>
    </div>
  );
}

// ══════════════════════════════════════════════════════
// FISKAL JUSTICE + PEMEKARAN
// ══════════════════════════════════════════════════════
const DAERAH=[
  {nama:"Tangerang Raya",prov:"Banten",kontribusi:62,terima:38,ket:"Industri terbesar Banten, tapi nambal Lebak & Pandeglang",status:"Kandidat pemekaran kuat"},
  {nama:"Bekasi",prov:"Jawa Barat",kontribusi:58,terima:41,ket:"Kontributor industri terbesar Jabar, tersedot ke daerah lain",status:"Dikaji DPR"},
  {nama:"Kutai Kartanegara",prov:"Kaltim",kontribusi:71,terima:29,ket:"Penghasil migas terbesar, DBH sebagian ke pusat & provinsi",status:"Pemekaran diusulkan"},
  {nama:"Surabaya",prov:"Jawa Timur",kontribusi:55,terima:45,ket:"Pusat ekonomi Jatim, subsidi ke kabupaten sekitar",status:"Kajian aktif"},
  {nama:"Deli Serdang",prov:"Sumatra Utara",kontribusi:49,terima:51,ket:"Relatif seimbang, tapi tren kontribusi terus naik",status:"Observasi"},
];

function FiskalJustice(){
  const [sel,setSel]=useState(0);
  const d=DAERAH[sel];
  return(
    <div id="pemekaran" style={{background:C.s1,borderTop:`1px solid ${C.border}`,borderBottom:`1px solid ${C.border}`}}>
      <Sec>
        <Label>Fiskal Justice Map</Label>
        <H2>KONTRIBUSI VS<br/><span style={{color:C.y}}>PENERIMAAN.</span></H2>
        <Sub>Daerah penghasil pajak dan industri terbesar — berapa yang mereka terima kembali? Data DJPK Kemenkeu vs PAD BPS.</Sub>
        <div style={{display:"grid",gridTemplateColumns:"1fr 1.4fr",gap:"2rem",marginTop:"1rem"}}>
          <div style={{display:"flex",flexDirection:"column",gap:".5rem"}}>
            {DAERAH.map((d,i)=>(
              <div key={i} onClick={()=>setSel(i)} style={{padding:"1rem 1.2rem",background:sel===i?`${C.y}10`:C.bg,border:`1px solid ${sel===i?C.y:C.border}`,cursor:"pointer",transition:"all .2s"}}>
                <div style={{display:"flex",justifyContent:"space-between",alignItems:"center"}}>
                  <span style={{fontWeight:700,fontSize:".9rem"}}>{d.nama}</span>
                  <Mono color={C.muted} size=".68rem">{d.prov}</Mono>
                </div>
                <div style={{display:"flex",gap:"1rem",marginTop:".5rem"}}>
                  <div style={{height:4,flex:1,background:C.border,position:"relative"}}>
                    <div style={{position:"absolute",top:0,left:0,height:"100%",width:`${d.kontribusi}%`,background:C.red,opacity:.7}}/>
                  </div>
                </div>
                <div style={{display:"flex",justifyContent:"space-between",marginTop:".3rem"}}>
                  <Mono color={C.red} size=".65rem">Kontribusi {d.kontribusi}%</Mono>
                  <Mono color={C.green} size=".65rem">Terima {d.terima}%</Mono>
                </div>
              </div>
            ))}
          </div>
          <Card>
            <div className="bebas" style={{fontSize:"2rem",color:C.y,marginBottom:".3rem"}}>{d.nama}</div>
            <Mono color={C.muted}>{d.prov}</Mono>
            <HR/>
            <Bar val={d.kontribusi} label="Kontribusi ke Provinsi" color={C.red}/>
            <Bar val={d.terima} label="Transfer Kembali" color={C.green}/>
            <div style={{marginTop:"1rem",padding:"1rem",background:C.bg,border:`1px solid ${C.border}`}}>
              <p style={{fontSize:".85rem",color:C.muted2,lineHeight:1.65,marginBottom:".6rem"}}>{d.ket}</p>
              <div style={{display:"flex",alignItems:"center",justifyContent:"space-between"}}>
                <Tag c={C.y}>Status</Tag>
                <Mono color={C.y}>{d.status}</Mono>
              </div>
            </div>
            <div style={{marginTop:"1rem",padding:"1rem",background:`${C.blue}08`,border:`1px solid ${C.blue}33`}}>
              <Mono color={C.blue} size=".65rem">SIMULASI: KALAU JADI PROVINSI SENDIRI</Mono>
              <p style={{fontSize:".82rem",color:C.muted2,marginTop:".4rem",lineHeight:1.6}}>
                Berdasarkan PAD dan PDRB {d.nama}, estimasi APBD mandiri: <strong style={{color:C.text}}>Rp 8-12 T/tahun</strong> — melampaui threshold kemandirian fiskal. Data lengkap: DJPK + BPS.
              </p>
            </div>
            <div className="mono" style={{marginTop:".8rem",fontSize:".62rem",color:C.muted}}>
              Sumber: DJPK Kemenkeu · BPS PDRB · PP No.78/2007 kriteria pemekaran
            </div>
          </Card>
        </div>
        <div style={{marginTop:"2rem"}}>
          <Mono color={C.muted}>300+ Usulan Pemekaran — Status di DPR</Mono>
          <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:1,background:C.border,border:`1px solid ${C.border}`,marginTop:".8rem"}}>
            {[["47","Dalam Kajian Aktif",C.y],["12","Masuk Prolegnas",C.blue],["8","Ditolak 5 Tahun Terakhir",C.red],["~300","Total Usulan Antre",C.muted2]].map(([n,l,c],i)=>(
              <div key={i} style={{background:C.bg,padding:"1.4rem"}}>
                <div className="bebas" style={{fontSize:"2rem",color:c,lineHeight:1,marginBottom:".3rem"}}>{n}</div>
                <Mono color={C.muted} size=".62rem">{l}</Mono>
              </div>
            ))}
          </div>
          <div className="mono" style={{marginTop:".6rem",fontSize:".62rem",color:C.muted,padding:".6rem",border:`1px solid ${C.border}`,background:`${C.y}04`}}>
            ⚠ Data status usulan pemekaran: DPR RI · Kemendagri · moratorium pemekaran belum dicabut resmi per Mei 2026.
          </div>
        </div>
      </Sec>
    </div>
  );
}

// ══════════════════════════════════════════════════════
// FOLLOW THE MONEY
// ══════════════════════════════════════════════════════
const FTM_STEPS=[
  {num:"01",icon:"🏛️",title:"APBN / APBD",desc:"Anggaran disetujui DPR, dialokasikan ke tiap kementerian dan daerah."},
  {num:"02",icon:"📋",title:"Rencana Pengadaan",desc:"Instansi buat rencana di SIRUP — siapa butuh apa, berapa anggarannya."},
  {num:"03",icon:"🏆",title:"Pemenang Tender",desc:"Tender di LPSE. Siapa menang? Terafiliasi pejabat? Cek AHU + LHKPN."},
  {num:"04",icon:"📦",title:"Produk / Supplier",desc:"Vendor beli dari mana? Lokal atau impor? Kalau impor, negara mana?"},
  {num:"05",icon:"🌏",title:"Tujuan Akhir",desc:"Uang berputar di Indonesia — atau keluar ke perusahaan asing?"},
];

function FollowMoney(){
  return(
    <Sec id="lacak">
      <Label>Follow The Money</Label>
      <H2>UANG RAKYAT<br/>PERGI KE MANA?</H2>
      <Sub>Trace perjalanan satu rupiah APBN — dari anggaran sampai tujuan akhirnya.</Sub>
      <div style={{display:"grid",gridTemplateColumns:"repeat(5,1fr)",gap:1,background:C.border,border:`1px solid ${C.border}`}}>
        {FTM_STEPS.map((s,i)=>(
          <div key={i} style={{background:C.s1,padding:"1.8rem",position:"relative"}}>
            <div className="bebas" style={{fontSize:"3rem",color:C.border2,lineHeight:1,marginBottom:".6rem"}}>{s.num}</div>
            <div style={{fontSize:"1.4rem",marginBottom:".5rem"}}>{s.icon}</div>
            <div style={{fontWeight:700,fontSize:".9rem",marginBottom:".4rem"}}>{s.title}</div>
            <p style={{fontSize:".78rem",color:C.muted2,lineHeight:1.6}}>{s.desc}</p>
            {i<4&&<div style={{position:"absolute",top:"50%",right:-1,transform:"translateY(-50%)",color:C.y,fontSize:"1rem",zIndex:2}}>▸</div>}
          </div>
        ))}
      </div>
    </Sec>
  );
}

// ══════════════════════════════════════════════════════
// P3DN
// ══════════════════════════════════════════════════════
const P3DN_DATA=[
  {ministry:"KEMENTERIAN KESEHATAN · Kemenperin 2024",name:"Belanja Produk Lokal Alkes",target:40,actual:48,verdict:"comply",note:"Naik dari 8% (2019) → 48% (2024). CT Scan masih 100% impor."},
  {ministry:"BGN — MAKAN BERGIZI · Data belum publik",name:"Komponen Lokal Program MBG",target:80,actual:null,verdict:"partial",note:"Target 80%+ lokal. Realisasi belum dipublikasi BGN — ini yang perlu didesak."},
  {ministry:"KEMENTERIAN PU · Data ilustrasi",name:"Material Konstruksi Infrastruktur",target:60,actual:null,verdict:"partial",note:"Data aktual dalam proses validasi dari LPSE + TKDN."},
  {ministry:"BSSN · Estimasi dari LPSE",name:"Infrastruktur IT & Siber",target:25,actual:9,verdict:"violate",note:"Mayoritas server dan perangkat IT masih impor. TKDN rata-rata 8-12%."},
  {ministry:"KEMENTERIAN PERTANIAN",name:"Alat Mesin Pertanian",target:50,actual:53,verdict:"comply",note:"Sedikit di atas target — sektor pertanian relatif lebih mandiri."},
  {ministry:"JANJI KAMPANYE 2024",name:"Target P3DN Prabowo-Gibran",target:95,actual:48,verdict:"partial",note:"Janji 95% produk lokal dalam pengadaan pemerintah. Realisasi rata-rata ~48%."},
];
const VERDICT_MAP={comply:[C.green,"✓ Comply"],partial:[C.y,"~ Parsial"],violate:[C.red,"✕ Tidak Comply"]};

function P3DN(){
  return(
    <div id="p3dn" style={{background:C.s1,borderTop:`1px solid ${C.border}`,borderBottom:`1px solid ${C.border}`}}>
      <Sec>
        <Label>Audit P3DN</Label>
        <H2>JANJI VS REALITA:<br/>PRODUK DALAM NEGERI.</H2>
        <Sub>Regulasi P3DN wajibkan pemerintah prioritaskan produk lokal. Ini hasilnya — diukur, bukan diklaim.</Sub>
        <div style={{display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:1,background:C.border,border:`1px solid ${C.border}`}}>
          {P3DN_DATA.map((p,i)=>{
            const [vc,vl]=VERDICT_MAP[p.verdict];
            return(
              <div key={i} style={{background:C.bg,padding:"1.6rem"}}>
                <Mono color={C.muted} size=".62rem">{p.ministry}</Mono>
                <div style={{fontWeight:700,fontSize:".9rem",margin:".5rem 0 1rem",lineHeight:1.4}}>{p.name}</div>
                <Bar val={p.target} label="Target" color={C.green}/>
                {p.actual!==null
                  ? <Bar val={p.actual} label="Aktual" color={p.actual>=p.target?C.green:C.red}/>
                  : <div style={{height:18,display:"flex",alignItems:"center"}}><Mono color={C.muted} size=".72rem">Aktual: Data belum dipublikasi</Mono></div>
                }
                <div style={{display:"flex",alignItems:"center",gap:".5rem",marginTop:".8rem",padding:".55rem .8rem",border:`1px solid ${vc}33`,background:`${vc}08`}}>
                  <span style={{fontSize:".8rem",fontWeight:600,color:vc}}>{vl}</span>
                </div>
                <p style={{fontSize:".72rem",color:C.muted,lineHeight:1.5,marginTop:".6rem"}}>{p.note}</p>
              </div>
            );
          })}
        </div>
      </Sec>
    </div>
  );
}

// ══════════════════════════════════════════════════════
// SEARCH
// ══════════════════════════════════════════════════════
const SAMPLE_DATA=[
  {name:"Pengadaan Server Data Center",inst:"BSSN · 2025",val:"Rp 31,7M",tkdn:"8%",tkdnColor:C.red,status:"Tender",flag:"impor"},
  {name:"Alat Kesehatan Diagnostik",inst:"Kemenkes · 2025",val:"Rp 48,3M",tkdn:"12%",tkdnColor:C.red,status:"Tender",flag:"absurd"},
  {name:"AC Ruang Pimpinan",inst:"Setda Kab. Morowali",val:"Rp 2,1M",tkdn:"—",tkdnColor:C.muted,status:"Tender",flag:"absurd"},
  {name:"Renovasi Jalan Trans Kalimantan",inst:"Kemen PU · 2025",val:"Rp 284M",tkdn:"71%",tkdnColor:C.green,status:"Selesai",flag:"ok"},
  {name:"Bahan Baku Susu MBG",inst:"BGN · 2025",val:"Rp 12,4M",tkdn:"38%",tkdnColor:C.y,status:"Proses",flag:"high"},
];

function Search(){
  const [q,setQ]=useState("");
  const [loading,setLoading]=useState(false);
  const search=()=>{setLoading(true);setTimeout(()=>setLoading(false),1200)};
  return(
    <Sec id="sumber">
      <Label>Telusuri Data</Label>
      <H2>CARI SENDIRI.<br/>VERIFIKASI SENDIRI.</H2>
      <div style={{display:"flex",border:`1px solid ${C.border2}`,background:C.s1,marginBottom:"1.5rem",transition:"border-color .2s"}}>
        <input value={q} onChange={e=>setQ(e.target.value)} onKeyDown={e=>e.key==="Enter"&&search()} placeholder="Cari kementerian, proyek, vendor, atau komoditas..."
          style={{flex:1,background:"none",border:"none",padding:"1rem 1.2rem",fontFamily:"'JetBrains Mono',monospace",fontSize:".83rem",color:C.text,outline:"none"}}/>
        <select style={{background:C.s2,border:"none",borderLeft:`1px solid ${C.border}`,color:C.muted2,padding:"0 1rem",fontFamily:"'JetBrains Mono',monospace",fontSize:".72rem",outline:"none",cursor:"pointer"}}>
          {["Semua","SIRUP","LPSE","TKDN","P3DN","BPK","Dana Desa"].map(o=><option key={o}>{o}</option>)}
        </select>
        <button onClick={search} style={{background:C.y,color:"#000",border:"none",padding:"1rem 1.6rem",fontFamily:"'JetBrains Mono',monospace",fontSize:".72rem",fontWeight:700,cursor:"pointer",textTransform:"uppercase",letterSpacing:".1em"}}>
          {loading?"Cari...":"Telusuri"}
        </button>
      </div>
      <div style={{border:`1px solid ${C.border}`}}>
        <div className="mono" style={{display:"grid",gridTemplateColumns:"2.5fr 1.2fr 1fr 1fr .9fr .9fr",background:C.border,padding:".65rem 1.2rem",gap:"1rem",fontSize:".6rem",color:C.muted,textTransform:"uppercase",letterSpacing:".1em"}}>
          {["Nama Paket","Instansi","Nilai","TKDN","Status","Flag"].map(h=><span key={h}>{h}</span>)}
        </div>
        {SAMPLE_DATA.map((r,i)=>(
          <div key={i} style={{display:"grid",gridTemplateColumns:"2.5fr 1.2fr 1fr 1fr .9fr .9fr",padding:".9rem 1.2rem",gap:"1rem",borderTop:`1px solid ${C.border}`,alignItems:"center",transition:"background .15s"}}
            onMouseEnter={e=>e.currentTarget.style.background=C.s1}
            onMouseLeave={e=>e.currentTarget.style.background="transparent"}>
            <div><div style={{fontSize:".83rem",fontWeight:600}}>{r.name}</div><Mono color={C.muted} size=".68rem">{r.inst}</Mono></div>
            <Mono>{r.val}</Mono>
            <Mono color={r.tkdnColor}>{r.tkdn}</Mono>
            <Mono color={C.muted2}>{r.status}</Mono>
            <Flag type={r.flag}/>
          </div>
        ))}
        <div className="mono" style={{padding:".65rem 1.2rem",borderTop:`1px solid ${C.border}`,fontSize:".62rem",color:C.muted,display:"flex",justifyContent:"space-between"}}>
          <span>5 dari 3.247.891 paket · Data: SIRUP LKPP + TKDN Kemenperin</span>
          <span>{new Date().toLocaleDateString("id-ID")}</span>
        </div>
      </div>
      <div className="mono" style={{marginTop:"1rem",border:`1px solid ${C.red}22`,background:`${C.red}04`,padding:"1rem 1.4rem",fontSize:".68rem",color:C.muted2,lineHeight:1.65}}>
        <strong style={{color:C.red}}>DISCLAIMER:</strong> Semua data dari portal resmi pemerintah RI. Label risiko adalah hasil analisis AI & metodologi terbuka — bukan vonis hukum. Setiap temuan perlu verifikasi lebih lanjut.
      </div>
    </Sec>
  );
}

// ══════════════════════════════════════════════════════
// SOURCES
// ══════════════════════════════════════════════════════
const SOURCES=[
  {tag:"Pengadaan",name:"SIRUP LKPP",url:"sirup.lkpp.go.id",desc:"3M+ paket · public API resmi"},
  {tag:"Tender",name:"LPSE LKPP",url:"lpse.lkpp.go.id",desc:"Pemenang tender & kontrak"},
  {tag:"Realisasi",name:"DJPb Kemenkeu",url:"djpb.kemenkeu.go.id",desc:"Belanja APBN per K/L"},
  {tag:"Audit",name:"BPK RI",url:"bpk.go.id",desc:"Hasil audit keuangan negara"},
  {tag:"Perusahaan",name:"AHU Online",url:"ahu.go.id",desc:"Profil & kepemilikan PT"},
  {tag:"Pejabat",name:"LHKPN KPK",url:"elhkpn.kpk.go.id",desc:"Harta kekayaan pejabat"},
  {tag:"Produk Lokal",name:"TKDN Kemenperin",url:"tkdn.kemenperin.go.id",desc:"Sertifikasi komponen lokal"},
  {tag:"Ekspor-Impor",name:"BPS Exim",url:"bps.go.id/exim",desc:"Impor per komoditas"},
  {tag:"Perdagangan",name:"INSW",url:"insw.go.id",desc:"Neraca & HS Code impor"},
  {tag:"Daerah",name:"SIPD Kemendagri",url:"sipd.kemendagri.go.id",desc:"APBD 514 kab/kota"},
  {tag:"Transfer",name:"DJPK Kemenkeu",url:"djpk.kemenkeu.go.id",desc:"DAU · DAK · DBH · Dana Desa"},
  {tag:"Terbuka",name:"Data.go.id",url:"data.go.id",desc:"Portal data terbuka RI"},
];

function Sources(){
  return(
    <Sec>
      <Label>Sumber Data</Label>
      <H2>SEMUA DARI<br/>NEGARA SENDIRI.</H2>
      <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:1,background:C.border,border:`1px solid ${C.border}`}}>
        {SOURCES.map((s,i)=>(
          <div key={i} style={{background:C.s1,padding:"1.4rem",transition:"background .2s"}}
            onMouseEnter={e=>e.currentTarget.style.background=C.s2}
            onMouseLeave={e=>e.currentTarget.style.background=C.s1}>
            <Mono color={C.y} size=".58rem">{s.tag.toUpperCase()}</Mono>
            <div style={{fontWeight:700,fontSize:".85rem",margin:".35rem 0 .2rem"}}>{s.name}</div>
            <Mono color={C.blue} size=".62rem">{s.url}</Mono>
            <p style={{fontSize:".72rem",color:C.muted2,marginTop:".4rem",lineHeight:1.5}}>{s.desc}</p>
          </div>
        ))}
      </div>
    </Sec>
  );
}

// ══════════════════════════════════════════════════════
// CTA + FOOTER
// ══════════════════════════════════════════════════════
function CTA(){
  return(
    <div style={{padding:"7rem 2rem",textAlign:"center",position:"relative",overflow:"hidden"}}>
      <div style={{position:"absolute",inset:0,background:`radial-gradient(ellipse 70% 60% at 50% 50%,${C.y}05,transparent 70%)`}}/>
      <div style={{position:"relative",zIndex:1}}>
        <Label><span style={{margin:"0 auto"}}>Open Source · Non-Komersial</span></Label>
        <h2 className="bebas" style={{fontSize:"clamp(3rem,7vw,6rem)",lineHeight:.95,marginBottom:"1.5rem"}}>
          INI BUKAN PRODUK.<br/><span style={{color:C.y}}>INI GERAKAN.</span>
        </h2>
        <p style={{color:C.muted2,marginBottom:"2.5rem",maxWidth:600,margin:"0 auto 2.5rem",lineHeight:1.75}}>
          Dibangun terbuka untuk jurnalis, akademisi, aktivis, dan warga biasa yang ingin tahu uangnya dipakai untuk apa — dan pergi ke mana.
        </p>
        <div style={{display:"flex",gap:"1rem",justifyContent:"center",flexWrap:"wrap"}}>
          <button style={{fontFamily:"'Schibsted Grotesk',sans-serif",fontWeight:700,fontSize:".88rem",background:C.y,color:"#000",padding:".85rem 2.2rem",border:"none",cursor:"pointer"}}>Buka Dashboard →</button>
          <button className="mono" style={{fontSize:".72rem",background:"none",border:`1px solid ${C.border2}`,color:C.muted2,padding:".85rem 1.8rem",cursor:"pointer",textTransform:"uppercase",letterSpacing:".08em"}}>Kontribusi GitHub</button>
          <button className="mono" style={{fontSize:".72rem",background:"none",border:`1px solid ${C.border2}`,color:C.muted2,padding:".85rem 1.8rem",cursor:"pointer",textTransform:"uppercase",letterSpacing:".08em"}}>API Docs</button>
        </div>
      </div>
    </div>
  );
}

function Footer(){
  return(
    <footer style={{borderTop:`1px solid ${C.border}`,padding:"1.6rem 2rem",display:"flex",justifyContent:"space-between",alignItems:"center",flexWrap:"wrap",gap:"1rem"}}>
      <div className="bebas" style={{fontSize:"1.3rem",letterSpacing:".08em"}}>Rupiah<span style={{color:C.y}}>Watch</span></div>
      <div style={{display:"flex",gap:"1.5rem"}}>
        {["GitHub","Metodologi","API Docs","Kontak","Whistleblower"].map(l=>(
          <a key={l} href="#" className="mono" style={{fontSize:".63rem",color:C.muted,textDecoration:"none",textTransform:"uppercase",letterSpacing:".1em",transition:"color .2s"}}
            onMouseEnter={e=>e.target.style.color=C.y} onMouseLeave={e=>e.target.style.color=C.muted}>{l}</a>
        ))}
      </div>
      <Mono color={C.muted} size=".63rem">Data publik · Open source · Non-komersial · 2026</Mono>
    </footer>
  );
}

// ══════════════════════════════════════════════════════
// APP
// ══════════════════════════════════════════════════════
export default function App(){
  return(
    <>
      <style>{css}</style>
      <Nav/>
      <Hero/>
      <Ticker/>
      <Stats/>
      <Tracker17/>
      <ProgramTracker/>
      <Features/>
      <ImportDep/>
      <FiskalJustice/>
      <FollowMoney/>
      <P3DN/>
      <Search/>
      <Sources/>
      <CTA/>
      <Footer/>
    </>
  );
}
