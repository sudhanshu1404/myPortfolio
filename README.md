/* ========= Basic interactions: theme toggle, typewriter, projects modal, particles ========= */

(() => {
  // Use provided details; nothing to ask from user.
  const TYPE_PHRASES = [
    "Python • SQL • Tableau",
    "Data Visualization & Insight",
    "Machine Learning basics",
    "Building practical dashboards"
  ];

  /* ---------- Theme toggle ---------- */
  const themeToggle = document.getElementById('themeToggle');
  function setTheme(isLight){
    if(isLight){
      document.documentElement.classList.add('light');
      themeToggle.setAttribute('aria-pressed','true');
    } else {
      document.documentElement.classList.remove('light');
      themeToggle.setAttribute('aria-pressed','false');
    }
    localStorage.setItem('site-theme', isLight ? 'light' : 'dark');
  }
  themeToggle?.addEventListener('click', () => {
    const isLight = !document.documentElement.classList.contains('light');
    setTheme(isLight);
  });
  // initialize theme from storage or system preference
  const saved = localStorage.getItem('site-theme');
  if(saved) setTheme(saved === 'light');
  else setTheme(window.matchMedia && window.matchMedia('(prefers-color-scheme: light)').matches);

  /* ---------- Typewriter effect ---------- */
  const twEl = document.getElementById('typewriter-text');
  let ti=0, ch=0, deleting=false, loopI=0;
  function tick(){
    const phrase = TYPE_PHRASES[loopI % TYPE_PHRASES.length];
    if(!deleting){
      twEl.textContent = phrase.slice(0, ch+1);
      ch++;
      if(ch===phrase.length){ deleting=true; setTimeout(tick,1200); return; }
    } else {
      twEl.textContent = phrase.slice(0, ch-1);
      ch--;
      if(ch===0){ deleting=false; loopI++; }
    }
    setTimeout(tick, deleting ? 60 : 80);
  }
  tick();

  /* ---------- Projects modal ---------- */
  const openBtns = document.querySelectorAll('.open-project');
  const modal = document.getElementById('projectModal');
  const modalTitle = document.getElementById('modalTitle');
  const modalDesc = document.getElementById('modalDesc');
  const modalLink = document.getElementById('modalLink');
  const closeModal = document.getElementById('closeModal');

  openBtns.forEach(btn =>{
    btn.addEventListener('click', (e)=>{
      const card = e.target.closest('.project');
      const title = card.dataset.title || card.querySelector('h4').innerText;
      const desc = card.dataset.desc || card.querySelector('p').innerText;
      const link = card.dataset.link || '#';
      modalTitle.textContent = title;
      modalDesc.textContent = desc;
      modalLink.href = link;
      modalLink.textContent = link === '#' ? 'No link available' : 'Open Project';
      modal.setAttribute('aria-hidden','false');
      document.body.style.overflow = 'hidden';
    });
  });

  closeModal.addEventListener('click', ()=>{ modal.setAttribute('aria-hidden','true'); document.body.style.overflow=''; });

  // close on ESC
  document.addEventListener('keydown', (e)=>{ if(e.key==='Escape'){ modal.setAttribute('aria-hidden','true'); document.body.style.overflow=''; } });

  /* ---------- Contact form UX (Formspree) ---------- */
  const contactForm = document.getElementById('contactForm');
  if(contactForm){
    contactForm.addEventListener('submit', (ev)=>{
      // Let Formspree handle the submission. Show quick feedback.
      const btn = contactForm.querySelector('button[type="submit"]');
      btn.disabled = true;
      btn.textContent = 'Sending...';
      setTimeout(()=>{ btn.textContent = 'Send'; btn.disabled = false; }, 3000);
    });
  }

  /* ---------- Adaptive canvas particles (simple) ---------- */
  const canvas = document.getElementById('particles-canvas');
  if(canvas && canvas.getContext){
    const ctx = canvas.getContext('2d');
    let w = canvas.width = innerWidth;
    let h = canvas.height = innerHeight;
    const particles = [];
    const PCOUNT = Math.round((w*h)/90000); // density
    for(let i=0;i<Math.max(20, PCOUNT); i++){
      particles.push({
        x: Math.random()*w,
        y: Math.random()*h,
        r: 0.6 + Math.random()*2.2,
        vx: (Math.random()-0.5)*0.4,
        vy: (Math.random()-0.5)*0.4,
        hue: 180 + Math.random()*120
      });
    }
    function resize(){ w=canvas.width=innerWidth; h=canvas.height=innerHeight; }
    addEventListener('resize', resize);

    function render(){
      ctx.clearRect(0,0,w,h);
      for(let p of particles){
        p.x += p.vx; p.y += p.vy;
        if(p.x < -20) p.x = w+20; if(p.x > w+20) p.x = -20;
        if(p.y < -20) p.y = h+20; if(p.y > h+20) p.y = -20;
        // glow
        ctx.beginPath();
        ctx.fillStyle = `rgba(120,200,220,0.04)`;
        ctx.arc(p.x,p.y,p.r*8,0,Math.PI*2); ctx.fill();
        ctx.beginPath();
        ctx.fillStyle = `rgba(255,255,255,0.08)`;
        ctx.arc(p.x,p.y,p.r*3,0,Math.PI*2); ctx.fill();
      }
      requestAnimationFrame(render);
    }
    render();
  }

  /* ---------- Small accessibility helpers ---------- */
  // smooth scroll for internal links
  document.querySelectorAll('a[href^="#"]').forEach(a=>{
    a.addEventListener('click', (e)=>{
      const t = document.querySelector(a.getAttribute('href'));
      if(t){ e.preventDefault(); t.scrollIntoView({behavior:'smooth', block:'start'}); }
    });
  });

})();
