<script>
  import { onMount } from 'svelte';

  let isSticky = false;

  onMount(() => {
    const handleScroll = () => {
      isSticky = window.scrollY > 20;
    };

    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  });
</script>

<svelte:head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <link href="https://fonts.googleapis.com/css2?family=Geist:wght@300;400;500;600;700&display=swap" rel="stylesheet" />
</svelte:head>

<style lang="scss">
  // --- VARIABLES ---
  $bg-base: #09090b;
  $bg-card: #111113;
  $primary: #3b82f6;
  $primary-glow: rgba(59, 130, 246, 0.15);
  $accent: #10b981;
  $text-main: #fafafa;
  $text-muted: #a1a1aa;
  $border: rgba(255, 255, 255, 0.08);
  $radius-lg: 16px;
  $transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

  :global(body) {
    background-color: $bg-base;
    color: $text-main;
    font-family: 'Geist', sans-serif;
    margin: 0;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
  }

  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 24px;
  }

  // --- HEADER & NAV ---
  .sticky-header {
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1000;
    transition: $transition;
    padding: 24px 0;

    &.isSticky {
      padding: 12px 0;
      backdrop-filter: blur(12px);
      background: rgba($bg-base, 0.8);
      border-bottom: 1px solid $border;
    }

    .nav {
      display: flex;
      justify-content: space-between;
      align-items: center;

      .logo {
        display: flex;
        align-items: center;
        gap: 12px;
        text-decoration: none;
        
        &-icon {
          width: 32px;
          height: 32px;
          background: linear-gradient(135deg, $primary, #60a5fa);
          border-radius: 8px;
          display: grid;
          place-items: center;
          color: white;
          font-weight: 800;
        }

        &-text {
          color: $text-main;
          font-weight: 700;
          font-size: 1.1rem;
          letter-spacing: -0.02em;
        }
      }

      .nav-links {
        display: flex;
        gap: 8px;

        a {
          color: $text-muted;
          text-decoration: none;
          font-size: 0.9rem;
          font-weight: 500;
          padding: 8px 16px;
          border-radius: 8px;
          transition: $transition;

          &:hover {
            color: $text-main;
            background: rgba(255, 255, 255, 0.05);
          }
        }
      }
    }
  }

  // --- HERO SECTION ---
  .hero {
    padding: 180px 0 100px;
    text-align: center;
    position: relative;

    &::before {
      content: "";
      position: absolute;
      top: 0;
      left: 50%;
      transform: translateX(-50%);
      width: 100%;
      height: 600px;
      background: radial-gradient(circle at top, $primary-glow, transparent 70%);
      pointer-events: none;
      z-index: -1;
    }

    h1 {
      font-size: clamp(2.5rem, 8vw, 4.5rem);
      font-weight: 800;
      letter-spacing: -0.04em;
      line-height: 1.1;
      margin-bottom: 24px;
      background: linear-gradient(to bottom, #fff 40%, rgba(255,255,255,0.5));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    p {
      font-size: 1.25rem;
      color: $text-muted;
      max-width: 650px;
      margin: 0 auto 40px;
    }

    .hero-buttons {
      display: flex;
      gap: 16px;
      justify-content: center;
    }
  }

  // --- BUTTONS ---
  .btn {
    padding: 12px 28px;
    border-radius: 10px;
    font-weight: 600;
    font-size: 0.95rem;
    text-decoration: none;
    transition: $transition;
    cursor: pointer;

    &.primary {
      background: $text-main;
      color: $bg-base;
      &:hover { transform: translateY(-2px); filter: brightness(0.9); }
    }

    &.outline {
      border: 1px solid $border;
      color: $text-main;
      background: rgba(255,255,255,0.03);
      &:hover { background: rgba(255,255,255,0.08); border-color: $text-muted; }
    }
  }

  // --- BENTO FEATURES ---
  .features-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
    padding-bottom: 100px;

    .card {
      background: $bg-card;
      border: 1px solid $border;
      border-radius: $radius-lg;
      padding: 32px;
      transition: $transition;
      position: relative;
      overflow: hidden;

      &:hover {
        border-color: rgba($primary, 0.4);
        transform: translateY(-4px);
        box-shadow: 0 20px 40px rgba(0,0,0,0.3);
      }

      .icon-box {
        width: 44px;
        height: 44px;
        background: rgba($primary, 0.1);
        border: 1px solid rgba($primary, 0.2);
        border-radius: 10px;
        display: grid;
        place-items: center;
        color: $primary;
        font-weight: 700;
        margin-bottom: 24px;
      }

      h3 {
        font-size: 1.25rem;
        margin-bottom: 12px;
        font-weight: 600;
      }

      p {
        color: $text-muted;
        font-size: 0.95rem;
        line-height: 1.6;
      }
    }

    // Bento sizes
    .large { grid-column: span 2; }
  }

  // --- CTA ---
  .cta-section {
    background: linear-gradient(135deg, #1e1e1e, $bg-base);
    border: 1px solid $border;
    border-radius: 24px;
    padding: 80px 40px;
    text-align: center;
    margin-bottom: 100px;

    h2 { font-size: 2.5rem; font-weight: 700; margin-bottom: 16px; }
    p { color: $text-muted; margin-bottom: 32px; }
  }

  // --- FOOTER ---
  .footer {
    border-top: 1px solid $border;
    padding: 80px 0 40px;
    
    .footer-grid {
      display: grid;
      grid-template-columns: 2fr 1fr 1fr 1fr;
      gap: 48px;
    }

    h4 { color: $text-main; margin-bottom: 20px; font-weight: 600; }
    ul { list-style: none; padding: 0; }
    li { margin-bottom: 12px; }
    a { color: $text-muted; text-decoration: none; transition: 0.2s; &:hover { color: $primary; }}
  }

  @media (max-width: 900px) {
    .features-grid { grid-template-columns: 1fr; .large { grid-column: span 1; }}
    .footer-grid { grid-template-columns: 1fr 1fr; }
  }
</style>

<header class="sticky-header" class:isSticky>
  <div class="nav-container">
    <nav class="nav">
      <a href="/" class="logo">
        <div class="logo-icon">L</div>
        <span class="logo-text">Lalye OS</span>
      </a>
      <div class="nav-links">
        <a href="#features">Features</a>
        <a href="#about">About</a>
        <a href="#contact">Contact</a>
      </div>
      <a href="/login" class="btn outline">Sign In</a>
    </nav>
  </div>
</header>

<div class="container">
  <section class="hero">
    <h1>L'intelligence financière <br/> sans la complexité.</h1>
    <p>Pilotez votre entreprise avec un OS financier moderne. Automatisez vos taxes, gérez vos flux et débloquez votre croissance.</p>
    <div class="hero-buttons">
      <a href="/dash" class="btn primary">Démarrer gratuitement</a>
      <a href="#features" class="btn outline">Voir la démo</a>
    </div>
  </section>

  <section class="features-grid" id="features">
    <div class="card large">
      <div class="icon-box">T</div>
      <h3>Fiscalité Intelligente</h3>
      <p>Optimisez vos déclarations avec notre moteur de calcul temps réel conforme aux dernières régulations mondiales.</p>
    </div>
    <div class="card">
      <div class="icon-box">I</div>
      <h3>Facturation</h3>
      <p>Envoyez des factures professionnelles qui se font payer 3x plus vite.</p>
    </div>
    <div class="card">
      <div class="icon-box">R</div>
      <h3>Reporting</h3>
      <p>Visualisez votre santé financière en un coup d'œil.</p>
    </div>
    <div class="card large">
      <div class="icon-box">C</div>
      <h3>Gestion de Trésorerie</h3>
      <p>Anticipez vos besoins de cash avec nos outils de prévision basés sur l'historique de vos flux réels.</p>
    </div>
  </section>

  <section class="cta-section">
    <h2>Prêt à transformer vos finances ?</h2>
    <p>Rejoignez les 500+ entreprises qui font confiance à Lalye pour leur gestion.</p>
    <a href="/dash" class="btn primary">Créer mon compte</a>
  </section>
</div>

<footer class="footer">
  <div class="container">
    <div class="footer-grid">
      <div>
        <span class="logo-text">Lalye OS</span>
        <p style="color: #6b7280; margin-top: 16px;">La plateforme financière nouvelle génération.</p>
      </div>
      <div>
        <h4>Produit</h4>
        <ul>
          <li><a href="/">Fonctionnalités</a></li>
          <li><a href="/">Tarifs</a></li>
        </ul>
      </div>
      <div>
        <h4>Société</h4>
        <ul>
          <li><a href="/">À propos</a></li>
          <li><a href="/">Blog</a></li>
        </ul>
      </div>
      <div>
        <h4>Légal</h4>
        <ul>
          <li><a href="/">Confidentialité</a></li>
          <li><a href="/">CGU</a></li>
        </ul>
      </div>
    </div>
  </div>
</footer>