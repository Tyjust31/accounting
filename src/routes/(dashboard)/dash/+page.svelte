<script>
  // --- SYNTAXE SVELTE 5 (RUNES) ---
  
  // États réactifs avec $state
  let selectedPeriod = $state("Mois");
  let searchQuery = $state("");
  let showNewModal = $state(false);

  // Données de base
  let revenue = $state(58600);
  let expenses = $state(41300);

  // Données dérivées avec $derived (remplace le $: de Svelte 4)
  // Calcul automatique de la santé et du bénéfice
  let profit = $derived(revenue - expenses);
  let healthScore = $derived(Math.round((profit / revenue) * 100));

  let transactions = $state([
    { id: "INV-123", client: "Tech Solutions", date: "04/04", montant: 4850, statut: "payée", tag: "Service" },
    { id: "INV-124", client: "Design Studio", date: "03/04", montant: 2300, statut: "attente", tag: "Design" },
    { id: "DEP-042", client: "Loyer Bureau", date: "01/04", montant: -2800, statut: "débitée", tag: "Fixe" }
  ]);

  let upcomingInvoices = $state([
    { fournisseur: "AWS Cloud", date: "15/03", montant: 89 },
    { fournisseur: "Loyer Bureau", date: "18/03", montant: 1200 }
  ]);

  // Filtrage réactif
  let filteredTransactions = $derived(
    transactions.filter(t => t.client.toLowerCase().includes(searchQuery.toLowerCase()))
  );

  // Fonctions d'actions
  function payInvoice(id) {
    upcomingInvoices = upcomingInvoices.filter((_, i) => i !== id);
    // Notification simulée
    console.log("Facture payée avec succès via Maketou");
  }

  // Effet pour logger les changements (remplace onMount/afterUpdate pour certains cas)
  $effect(() => {
    if (healthScore < 10) {
      console.warn("Alerte : Trésorerie critique !");
    }
  });
</script>

<div class="dashboard-wrapper">
  <aside class="side-nav">
    <div class="brand">L</div>
    
    <div class="health-indicator">
      <div class="glow-ring" style="--p: {healthScore}">
        <span class="value">{healthScore}%</span>
      </div>
      <p>Santé</p>
    </div>

    <nav class="icons">
      <button class="active">🏠</button>
      <button>📊</button>
      <button>💳</button>
      <button>⚙️</button>
    </nav>
  </aside>

  <main class="content">
    <header class="top-bar">
      <div class="welcome">
        <h1>Dashboard <span class="v5">Svelte 5</span></h1>
        <p>Analyse des flux de {selectedPeriod.toLowerCase()}</p>
      </div>

      <div class="actions">
        <div class="segmented-control">
          {#each ['Mois', 'Trimestre', 'Année'] as p}
            <button 
              class:active={selectedPeriod === p} 
              onclick={() => selectedPeriod = p}
            >
              {p}
            </button>
          {/each}
        </div>
        <button class="add-btn" onclick={() => showNewModal = true}>
          <span>+</span> Nouveau
        </button>
      </div>
    </header>

    <section class="stats-grid">
      <div class="stat-card">
        <label>Chiffre d'Affaires</label>
        <div class="number">{revenue.toLocaleString()} €</div>
        <span class="trend up">+12.4%</span>
      </div>
      <div class="stat-card">
        <label>Bénéfice Net</label>
        <div class="number">{profit.toLocaleString()} €</div>
        <span class="trend {profit > 0 ? 'up' : 'down'}">{healthScore}% marge</span>
      </div>
      <div class="stat-card highlight">
        <label>Prochain Prélèvement</label>
        <div class="number">18 Mars</div>
        <span class="detail">Loyer Bureau (1 200 €)</span>
      </div>
    </section>

    <div class="main-grid">
      <section class="panel">
        <div class="panel-head">
          <h2>Flux Récents</h2>
          <input type="text" bind:value={searchQuery} placeholder="Rechercher un client..." />
        </div>
        
        <div class="table-container">
          <table>
            <thead>
              <tr>
                <th>Client</th>
                <th>Catégorie</th>
                <th>Montant</th>
                <th>Statut</th>
              </tr>
            </thead>
            <tbody>
              {#each filteredTransactions as t}
                <tr class="row">
                  <td><strong>{t.client}</strong><br/><small>{t.date}</small></td>
                  <td><span class="tag">{t.tag}</span></td>
                  <td class="amt" class:neg={t.montant < 0}>{t.montant} €</td>
                  <td><span class="badge {t.statut}">{t.statut}</span></td>
                </tr>
              {/each}
            </tbody>
          </table>
        </div>
      </section>

      <section class="panel compact">
        <h2>À régler</h2>
        <div class="invoice-list">
          {#each upcomingInvoices as inv, i}
            <div class="invoice-item">
              <div class="info">
                <strong>{inv.fournisseur}</strong>
                <span>{inv.montant} €</span>
              </div>
              <button onclick={() => payInvoice(i)}>Payer</button>
            </div>
          {/each}
          {#if upcomingInvoices.length === 0}
            <p class="empty">Toutes les factures sont réglées ! 🎉</p>
          {/if}
        </div>

        <hr />

        <h2>Rapports Rapides</h2>
        <button class="action-link">Télécharger le bilan Q1</button>
        <button class="action-link">Prévisionnel Taxes 2026</button>
      </section>
    </div>
  </main>
</div>

<style lang="scss">
  // --- DESIGN SYSTEM ---
  $bg: #0b0e14;
  $surface: #161b22;
  $border: rgba(255, 255, 255, 0.08);
  $primary: #3fb950; // Vert Lalye
  $accent: #2f81f7; // Bleu Svelte
  $text: #adbac7;

  :global(body) {
    background-color: $bg;
    color: $text;
    margin: 0;
    font-family: 'Inter', system-ui, sans-serif;
  }

  .dashboard-wrapper {
    display: flex;
    min-height: 100vh;
  }

  // --- SIDE NAV ---
  .side-nav {
    width: 80px;
    background: $surface;
    border-right: 1px solid $border;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px 0;
    position: fixed;
    height: 100vh;

    .brand {
      width: 40px;
      height: 40px;
      background: $primary;
      color: white;
      border-radius: 12px;
      display: grid;
      place-items: center;
      font-weight: 900;
      font-size: 20px;
      margin-bottom: 40px;
    }

    .health-indicator {
      text-align: center;
      margin-bottom: 40px;
      .glow-ring {
        width: 50px;
        height: 50px;
        border-radius: 50%;
        border: 3px solid $border;
        display: grid;
        place-items: center;
        position: relative;
        &::after {
            content: '';
            position: absolute;
            inset: -3px;
            border-radius: 50%;
            border: 3px solid $primary;
            clip-path: inset(0 0 50% 0); // Simplifié pour l'exemple
        }
        .value { font-size: 11px; font-weight: bold; color: white; }
      }
      p { font-size: 9px; margin-top: 8px; text-transform: uppercase; }
    }

    .icons {
      display: flex;
      flex-direction: column;
      gap: 20px;
      button {
        background: none; border: none; font-size: 20px; cursor: pointer; filter: grayscale(1);
        &.active { filter: grayscale(0); background: rgba(255,255,255,0.05); border-radius: 8px; padding: 10px; }
      }
    }
  }

  // --- MAIN CONTENT ---
  .content {
    margin-left: 80px;
    flex: 1;
    padding: 40px;
  }

  .top-bar {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 40px;

    h1 { color: white; margin: 0; font-size: 28px; 
      .v5 { font-size: 12px; background: $accent; padding: 2px 6px; border-radius: 4px; vertical-align: top;}
    }

    .actions {
      display: flex;
      gap: 15px;
      
      .segmented-control {
        background: $surface;
        padding: 4px;
        border-radius: 10px;
        border: 1px solid $border;
        button {
          background: none; border: none; color: $text; padding: 8px 16px; border-radius: 7px; cursor: pointer;
          &.active { background: #21262d; color: white; box-shadow: 0 2px 4px rgba(0,0,0,0.2); }
        }
      }

      .add-btn {
        background: $primary;
        color: white;
        border: none;
        padding: 0 20px;
        border-radius: 10px;
        font-weight: 600;
        cursor: pointer;
        &:hover { opacity: 0.9; }
      }
    }
  }

  // --- STATS GRID ---
  .stats-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
    margin-bottom: 40px;

    .stat-card {
      background: $surface;
      padding: 24px;
      border-radius: 16px;
      border: 1px solid $border;
      position: relative;
      overflow: hidden;

      label { font-size: 12px; text-transform: uppercase; letter-spacing: 0.5px; }
      .number { font-size: 32px; font-weight: 700; color: white; margin: 10px 0; }
      .trend { font-size: 12px; font-weight: 600; 
        &.up { color: $primary; }
        &.down { color: #f85149; }
      }
      
      &.highlight {
        background: linear-gradient(135deg, #1e2530, #161b22);
        border-color: $accent;
        .number { color: $accent; }
      }
    }
  }

  // --- PANELS ---
  .main-grid {
    display: grid;
    grid-template-columns: 2fr 1fr;
    gap: 20px;
  }

  .panel {
    background: $surface;
    border-radius: 16px;
    border: 1px solid $border;
    padding: 24px;

    .panel-head {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
      input { background: $bg; border: 1px solid $border; color: white; padding: 8px 12px; border-radius: 8px; width: 250px; }
    }

    h2 { font-size: 18px; color: white; margin-bottom: 20px; }
  }

  table {
    width: 100%;
    border-collapse: collapse;
    th { text-align: left; font-size: 12px; padding: 10px; border-bottom: 1px solid $border; }
    td { padding: 15px 10px; border-bottom: 1px solid rgba(255,255,255,0.02); }
    .amt { font-weight: bold; &.neg { color: #f85149; }}
    .tag { background: #21262d; padding: 4px 8px; border-radius: 6px; font-size: 11px; }
    .badge { font-size: 10px; padding: 4px 8px; border-radius: 4px; text-transform: uppercase; font-weight: bold;
      &.payée { background: rgba($primary, 0.1); color: $primary; }
      &.attente { background: rgba(255, 165, 0, 0.1); color: orange; }
    }
  }

  .invoice-list {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin-bottom: 30px;
    
    .invoice-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: $bg;
      padding: 12px;
      border-radius: 10px;
      .info { display: flex; flex-direction: column; strong { color: white; font-size: 14px; } span { font-size: 12px; }}
      button { background: #21262d; border: 1px solid $border; color: white; padding: 6px 12px; border-radius: 6px; cursor: pointer; &:hover { background: $primary; }}
    }
  }

  .action-link {
    display: block;
    width: 100%;
    text-align: left;
    background: none; border: none; color: $accent; padding: 8px 0; cursor: pointer; font-size: 14px;
    &:hover { text-decoration: underline; }
  }
</style>