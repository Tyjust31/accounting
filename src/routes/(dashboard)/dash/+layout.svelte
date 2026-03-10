<script lang="ts">
  import type { Snippet } from 'svelte';
  
  // -- PROPS & CONFIGURATION --
  let { 
    children, 
    appName = "Lalye Financial",
    sidebarWidth = "280px" 
  }: { 
    children: Snippet; 
    appName?: string; 
    sidebarWidth?: string 
  } = $props();

  // -- ÉTAT RÉACTIF (RUNES) --
  let sidebarOpen = $state(false);
  let innerWidth = $state(0);
  
  // Navigation avec état de sélection
  let currentPath = $state("/dashboard"); // Dans un vrai projet, lié à ton routeur

  const navItems = [
    { id: 'dash', icon: "grid", label: "Tableau de Bord", path: "/dashboard" },
    { id: 'trans', icon: "activity", label: "Transactions", path: "/transactions" },
    { id: 'bill', icon: "credit-card", label: "Facturation", path: "/billing" },
    { id: 'team', icon: "users", label: "Collaborateurs", path: "/team" },
    { id: 'sett', icon: "settings", label: "Configuration", path: "/settings" }
  ];

  // -- LOGIQUE --
  const toggleSidebar = () => sidebarOpen = !sidebarOpen;

  // On ferme la sidebar automatiquement si on repasse sur desktop
  $effect(() => {
    if (innerWidth > 768 && sidebarOpen) sidebarOpen = false;
  });
</script>

<svelte:window bind:innerWidth />

<div class="app-layout" style="--sb-width: {sidebarWidth}">
  
  {#if sidebarOpen}
    <button class="overlay" onclick={toggleSidebar} aria-label="Fermer le menu"></button>
  {/if}

  <aside class="sidebar" class:mobile-open={sidebarOpen}>
    <div class="sidebar-brand">
      <div class="logo-box">L</div>
      <h2> <a class="app" href="/">{appName}</a></h2>
    </div>

    <nav class="nav-links">
      {#each navItems as item}
        <a 
          href={item.path} 
          class="nav-item" 
          class:active={currentPath === item.path}
          onclick={(e) => {
            currentPath = item.path;
            if (innerWidth < 768) sidebarOpen = false;
          }}
        >
          <span class="nav-icon">{item.icon === 'grid' ? '⊞' : item.icon === 'activity' ? '⚡' : '⚙️'}</span>
          <span class="nav-label">{item.label}</span>
        </a>
      {/each}
    </nav>

    <div class="sidebar-footer">
      <div class="user-card">
        <img src="https://ui-avatars.com/api/?name=John+Doe&background=3b82f6&color=fff" alt="Avatar" />
        <div class="details">
          <p class="name">John Doe</p>
          <p class="role">Admin Plan Pro</p>
        </div>
      </div>
    </div>
  </aside>

  <main class="main-viewport">
    <header class="top-nav">
      <button class="burger" onclick={toggleSidebar}>
        <span></span><span></span><span></span>
      </button>
      <div class="breadcrumb">
        <span>Finance</span> / <strong>{navItems.find(i => i.path === currentPath)?.label}</strong>
      </div>
      <div class="top-actions">
        <button class="notif-btn">🔔 <span class="dot"></span></button>
      </div>
    </header>

    <div class="scroll-content">
      {@render children()}
    </div>
  </main>
</div>

<style lang="scss">
  // Palette de couleurs Deep Night
  $bg-dark: #0f172a;
  $sidebar-bg: #1e293b;
  $accent: #3b82f6;
  $text-main: #f1f5f9;
  $text-dim: #94a3b8;
  $border: rgba(255, 255, 255, 0.06);




  .app{
     color: $text-main;
     text-decoration: none;
  }

  .app-layout {
    display: flex;
    background: $bg-dark;
    color: $text-main;
    min-height: 100vh;
  }

  .overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.5);
    backdrop-filter: blur(4px);
    z-index: 50;
    border: none;
    width: 100%;
  }

  // --- SIDEBAR ---
  .sidebar {
    width: var(--sb-width);
    background: $sidebar-bg;
    border-right: 1px solid $border;
    display: flex;
    flex-direction: column;
    z-index: 100;
    transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);

    @media (max-width: 768px) {
      position: fixed;
      height: 100vh;
      transform: translateX(-100%);
      &.mobile-open { transform: translateX(0); }
    }
  }

  .sidebar-brand {
    padding: 2rem 1.5rem;
    display: flex;
    align-items: center;
    gap: 12px;
    .logo-box {
      background: $accent;
      width: 32px;
      height: 32px;
      border-radius: 8px;
      display: grid;
      place-items: center;
      font-weight: bold;
    }
    h2 { font-size: 1.1rem; letter-spacing: -0.5px; margin: 0; }
  }

  .nav-links {
    flex: 1;
    padding: 0 1rem;
    .nav-item {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 12px 16px;
      color: $text-dim;
      text-decoration: none;
      border-radius: 12px;
      margin-bottom: 4px;
      transition: 0.2s;
      
      &:hover { background: rgba(255,255,255,0.03); color: white; }
      &.active { background: $accent; color: white; box-shadow: 0 4px 12px rgba($accent, 0.2); }
    }
  }

  // --- MAIN AREA ---
  .main-viewport {
    flex: 1;
    display: flex;
    flex-direction: column;
    height: 100vh;
    overflow: hidden;
  }

  .top-nav {
    height: 64px;
    border-bottom: 1px solid $border;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 2rem;
    background: rgba($bg-dark, 0.8);
    backdrop-filter: blur(12px);

    .burger {
      display: none;
      flex-direction: column;
      gap: 4px;
      background: none; border: none;
      span { width: 20px; height: 2px; background: white; }
      @media (max-width: 768px) { display: flex; }
    }
  }

  .scroll-content {
    flex: 1;
    overflow-y: auto;
    padding: 2rem;
    scrollbar-width: thin;
    scrollbar-color: $border transparent;
  }

  .user-card {
    background: rgba(0,0,0,0.2);
    margin: 1rem;
    padding: 12px;
    border-radius: 12px;
    display: flex;
    align-items: center;
    gap: 12px;
    img { width: 36px; height: 36px; border-radius: 50%; }
    .name { font-size: 14px; font-weight: 600; margin: 0; }
    .role { font-size: 11px; color: $text-dim; margin: 0; }
  }

</style>