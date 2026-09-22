# finance-trading
site web pour la reussite en trading


import React, { useState, useMemo } from 'react';
import { 
  BarChart3, 
  TrendingUp, 
  BookOpen, 
  Calculator, 
  DollarSign, 
  PieChart, 
  Menu, 
  X, 
  ChevronRight, 
  Plus, 
  Trash2, 
  ArrowUpRight, 
  ArrowDownRight, 
  ShieldCheck, 
  Activity,
  Layers,
  Search,
  CheckCircle2,
  Calendar,
  Zap
} from 'lucide-react';
import { AreaChart, Area, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer } from 'recharts';

export default function App() {
  const [sidebarOpen, setSidebarOpen] = useState(true);
  const [activeTab, setActiveTab] = useState('journal');

  // DONNÉES DU PLAN 90 JOURS (10 USD de départ)
  const baseData = [
    { day: 1, funds: 10.00, profit: 0.60, total: 10.60 },
    { day: 2, funds: 10.60, profit: 0.64, total: 11.24 },
    { day: 3, funds: 11.24, profit: 0.67, total: 11.91 },
    { day: 4, funds: 11.91, profit: 0.71, total: 12.62 },
    { day: 5, funds: 12.62, profit: 0.76, total: 13.38 },
    { day: 10, funds: 16.89, profit: 1.01, total: 17.91 },
    { day: 15, funds: 22.61, profit: 1.36, total: 23.97 },
    { day: 20, funds: 30.26, profit: 1.82, total: 32.07 },
    { day: 25, funds: 40.49, profit: 2.43, total: 42.92 },
    { day: 30, funds: 54.18, profit: 3.25, total: 57.43 },
    { day: 40, funds: 97.04, profit: 5.82, total: 102.86 },
    { day: 50, funds: 173.78, profit: 10.43, total: 184.20 },
    { day: 60, funds: 311.20, profit: 18.67, total: 329.86 },
    { day: 70, funds: 557.32, profit: 33.44, total: 590.76 },
    { day: 80, funds: 996.08, profit: 59.68, total: 1057.96 },
    { day: 90, funds: 1787.40, profit: 107.24, total: 1894.64 }
  ];

  // Calcul dynamique des 90 jours
  const full90DaysData = useMemo(() => {
    let data = [];
    let currentFunds = 10.0;
    const dailyRate = 0.06;

    for (let day = 1; day <= 90; day++) {
      const profit = currentFunds * dailyRate;
      const total = currentFunds + profit;
      data.push({
        day,
        funds: parseFloat(currentFunds.toFixed(2)),
        profit: parseFloat(profit.toFixed(2)),
        total: parseFloat(total.toFixed(2))
      });
      currentFunds = total;
    }
    return data;
  }, []);

  // CALCULATEUR PERSONNALISÉ
  const [customCapital, setCustomCapital] = useState(100);
  const [customRate, setCustomRate] = useState(6);
  const [customDays, setCustomDays] = useState(30);

  const calculatedResults = useMemo(() => {
    let funds = parseFloat(customCapital) || 0;
    const rate = (parseFloat(customRate) || 0) / 100;
    const days = parseInt(customDays) || 1;
    let list = [];

    for (let d = 1; d <= days; d++) {
      const profit = funds * rate;
      const total = funds + profit;
      list.push({ day: d, funds: funds.toFixed(2), profit: profit.toFixed(2), total: total.toFixed(2) });
      funds = total;
    }
    return list;
  }, [customCapital, customRate, customDays]);

  // JOURNAL DE TRADING STATE
  const [trades, setTrades] = useState([
    { id: 1, pair: 'BTC/USDT', type: 'BUY', entry: 62000, exit: 64500, amount: 500, pnl: 201.61, date: '2026-09-20', notes: 'Cassure de résistance confirmée' },
    { id: 2, pair: 'EUR/USD', type: 'SELL', entry: 1.0850, exit: 1.0810, amount: 1000, pnl: 36.86, date: '2026-09-21', notes: 'NFP Bearish' },
    { id: 3, pair: 'ETH/USDT', type: 'BUY', entry: 2750, exit: 2680, amount: 300, pnl: -7.63, date: '2026-09-22', notes: 'Stop Loss touché' }
  ]);

  const [newTrade, setNewTrade] = useState({
    pair: '',
    type: 'BUY',
    entry: '',
    exit: '',
    amount: '',
    notes: ''
  });

  const handleAddTrade = (e) => {
    e.preventDefault();
    if (!newTrade.pair || !newTrade.entry || !newTrade.exit || !newTrade.amount) return;

    const entry = parseFloat(newTrade.entry);
    const exit = parseFloat(newTrade.exit);
    const amount = parseFloat(newTrade.amount);
    
    let pnl = 0;
    if (newTrade.type === 'BUY') {
      pnl = ((exit - entry) / entry) * amount;
    } else {
      pnl = ((entry - exit) / entry) * amount;
    }

    const tradeObj = {
      id: Date.now(),
      pair: newTrade.pair.toUpperCase(),
      type: newTrade.type,
      entry,
      exit,
      amount,
      pnl: parseFloat(pnl.toFixed(2)),
      date: new Date().toISOString().split('T')[0],
      notes: newTrade.notes
    };

    setTrades([tradeObj, ...trades]);
    setNewTrade({ pair: '', type: 'BUY', entry: '', exit: '', amount: '', notes: '' });
  };

  const handleDeleteTrade = (id) => {
    setTrades(trades.filter(t => t.id !== id));
  };

  // Statstiques Journal
  const totalPnL = trades.reduce((acc, t) => acc + t.pnl, 0);
  const winningTrades = trades.filter(t => t.pnl > 0).length;
  const winRate = trades.length > 0 ? ((winningTrades / trades.length) * 100).toFixed(1) : 0;

  return (
    <div className="flex h-screen bg-[#0d0f17] text-slate-100 font-sans overflow-hidden">
      
      {/* SIDEBAR STYLE GEMINI */}
      <aside 
        className={`${
          sidebarOpen ? 'w-64' : 'w-20'
        } bg-[#131622] border-r border-slate-800/80 transition-all duration-300 flex flex-col justify-between z-30 shrink-0`}
      >
        <div>
          {/* Header Sidebar */}
          <div className="p-4 flex items-center justify-between">
            {sidebarOpen ? (
              <div className="flex items-center space-x-3">
                <div className="w-9 h-9 rounded-xl bg-gradient-to-tr from-cyan-500 to-blue-600 flex items-center justify-[#ffffff] font-extrabold text-xl shadow-lg shadow-cyan-500/20 justify-center">
                  D
                </div>
                <div>
                  <h1 className="font-bold text-base tracking-wider bg-gradient-to-r from-white via-slate-200 to-slate-400 bg-clip-text text-transparent">
                    DIALLO
                  </h1>
                  <p className="text-[10px] text-cyan-400 font-medium tracking-widest uppercase">TRADING PRO</p>
                </div>
              </div>
            ) : (
              <div className="w-9 h-9 rounded-xl bg-gradient-to-tr from-cyan-500 to-blue-600 flex items-center justify-center font-extrabold text-xl shadow-lg shadow-cyan-500/20 mx-auto">
                D
              </div>
            )}
            
            <button 
              onClick={() => setSidebarOpen(!sidebarOpen)}
              className="p-1.5 rounded-lg hover:bg-slate-800 text-slate-400 hover:text-white transition-colors"
            >
              <Menu size={20} />
            </button>
          </div>

          {/* Navigation Items */}
          <nav className="mt-6 px-3 space-y-1.5">
            <button
              onClick={() => setActiveTab('journal')}
              className={`w-full flex items-center ${sidebarOpen ? 'justify-start space-x-3 px-3.5' : 'justify-center px-0'} py-3 rounded-xl transition-all ${
                activeTab === 'journal' 
                  ? 'bg-gradient-to-r from-cyan-500/20 to-blue-500/10 text-cyan-400 border border-cyan-500/30 font-medium' 
                  : 'text-slate-400 hover:bg-slate-800/60 hover:text-slate-200'
              }`}
            >
              <BookOpen size={20} className={activeTab === 'journal' ? 'text-cyan-400' : 'text-slate-400'} />
              {sidebarOpen && <span>Journal de Trading</span>}
            </button>

            <button
              onClick={() => setActiveTab('plan90')}
              className={`w-full flex items-center ${sidebarOpen ? 'justify-start space-x-3 px-3.5' : 'justify-center px-0'} py-3 rounded-xl transition-all ${
                activeTab === 'plan90' 
                  ? 'bg-gradient-to-r from-cyan-500/20 to-blue-500/10 text-cyan-400 border border-cyan-500/30 font-medium' 
                  : 'text-slate-400 hover:bg-slate-800/60 hover:text-slate-200'
              }`}
            >
              <TrendingUp size={20} className={activeTab === 'plan90' ? 'text-cyan-400' : 'text-slate-400'} />
              {sidebarOpen && <span>Plan 10$ (90 Jours)</span>}
            </button>

            <button
              onClick={() => setActiveTab('calculator')}
              className={`w-full flex items-center ${sidebarOpen ? 'justify-start space-x-3 px-3.5' : 'justify-center px-0'} py-3 rounded-xl transition-all ${
                activeTab === 'calculator' 
                  ? 'bg-gradient-to-r from-cyan-500/20 to-blue-500/10 text-cyan-400 border border-cyan-500/30 font-medium' 
                  : 'text-slate-400 hover:bg-slate-800/60 hover:text-slate-200'
              }`}
            >
              <Calculator size={20} className={activeTab === 'calculator' ? 'text-cyan-400' : 'text-slate-400'} />
              {sidebarOpen && <span>Calculateur Sur-Mesure</span>}
            </button>
          </nav>
        </div>

        {/* Footer Sidebar */}
        {sidebarOpen && (
          <div className="p-4 border-t border-slate-800/80 bg-slate-900/40">
            <div className="flex items-center space-x-3">
              <div className="w-8 h-8 rounded-full bg-slate-800 flex items-center justify-center text-xs font-bold text-slate-300">
                DT
              </div>
              <div className="text-xs">
                <p className="font-semibold text-slate-200">Trader Account</p>
                <p className="text-emerald-400 font-medium text-[10px]">● Connecté</p>
              </div>
            </div>
          </div>
        )}
      </aside>

      {/* CONTENU PRINCIPAL */}
      <main className="flex-1 overflow-y-auto bg-[#0d0f17] p-4 lg:p-8">
        
        {/* TAB 1 : JOURNAL DE TRADING */}
        {activeTab === 'journal' && (
          <div className="max-w-7xl mx-auto space-y-6">
            <div className="flex flex-col md:flex-row md:items-center justify-between gap-4">
              <div>
                <h2 className="text-2xl lg:text-3xl font-extrabold text-white flex items-center gap-3">
                  <BookOpen className="text-cyan-400" /> Journal de Trading
                </h2>
                <p className="text-slate-400 text-sm mt-1">Suivez vos performances, vos gains et analysez vos stratégies.</p>
              </div>

              {/* Badges Statistiques */}
              <div className="grid grid-cols-3 gap-3">
                <div className="bg-[#131622] p-3 rounded-xl border border-slate-800 text-center">
                  <p className="text-[11px] text-slate-400 uppercase font-semibold">Total PnL</p>
                  <p className={`text-base font-bold ${totalPnL >= 0 ? 'text-emerald-400' : 'text-rose-400'}`}>
                    {totalPnL >= 0 ? `+${totalPnL.toFixed(2)}$` : `${totalPnL.toFixed(2)}$`}
                  </p>
                </div>
                <div className="bg-[#131622] p-3 rounded-xl border border-slate-800 text-center">
                  <p className="text-[11px] text-slate-400 uppercase font-semibold">Win Rate</p>
                  <p className="text-base font-bold text-cyan-400">{winRate}%</p>
                </div>
                <div className="bg-[#131622] p-3 rounded-xl border border-slate-800 text-center">
                  <p className="text-[11px] text-slate-400 uppercase font-semibold">Trades</p>
                  <p className="text-base font-bold text-white">{trades.length}</p>
                </div>
              </div>
            </div>

            {/* Formulaire & Table */}
            <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
              
              {/* Formulaire d'ajout */}
              <div className="bg-[#131622] p-5 rounded-2xl border border-slate-800/80 shadow-xl h-fit">
                <h3 className="text-lg font-semibold text-white mb-4 flex items-center gap-2">
                  <Plus className="text-cyan-400" size={18} /> Ajouter une Position
                </h3>

                <form onSubmit={handleAddTrade} className="space-y-4">
                  <div>
                    <label className="text-xs text-slate-400 font-medium block mb-1">Actif / Paire</label>
                    <input 
                      type="text" 
                      placeholder="ex: BTC/USDT, EUR/USD" 
                      value={newTrade.pair}
                      onChange={(e) => setNewTrade({...newTrade, pair: e.target.value})}
                      className="w-full bg-[#0d0f17] border border-slate-700/80 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-cyan-500"
                    />
                  </div>

                  <div className="grid grid-cols-2 gap-3">
                    <div>
                      <label className="text-xs text-slate-400 font-medium block mb-1">Direction</label>
                      <select 
                        value={newTrade.type}
                        onChange={(e) => setNewTrade({...newTrade, type: e.target.value})}
                        className="w-full bg-[#0d0f17] border border-slate-700/80 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-cyan-500"
                      >
                        <option value="BUY">BUY (Achat)</option>
                        <option value="SELL">SELL (Vente)</option>
                      </select>
                    </div>
                    <div>
                      <label className="text-xs text-slate-400 font-medium block mb-1">Capital ($)</label>
                      <input 
                        type="number" 
                        placeholder="100" 
                        value={newTrade.amount}
                        onChange={(e) => setNewTrade({...newTrade, amount: e.target.value})}
                        className="w-full bg-[#0d0f17] border border-slate-700/80 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-cyan-500"
                      />
                    </div>
                  </div>

                  <div className="grid grid-cols-2 gap-3">
                    <div>
                      <label className="text-xs text-slate-400 font-medium block mb-1">Prix d'Entrée</label>
                      <input 
                        type="number" 
                        step="any" 
                        placeholder="60000" 
                        value={newTrade.entry}
                        onChange={(e) => setNewTrade({...newTrade, entry: e.target.value})}
                        className="w-full bg-[#0d0f17] border border-slate-700/80 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-cyan-500"
                      />
                    </div>
                    <div>
                      <label className="text-xs text-slate-400 font-medium block mb-1">Prix de Sortie</label>
                      <input 
                        type="number" 
                        step="any" 
                        placeholder="62000" 
                        value={newTrade.exit}
                        onChange={(e) => setNewTrade({...newTrade, exit: e.target.value})}
                        className="w-full bg-[#0d0f17] border border-slate-700/80 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-cyan-500"
                      />
                    </div>
                  </div>

                  <div>
                    <label className="text-xs text-slate-400 font-medium block mb-1">Remarques / Leçons</label>
                    <textarea 
                      rows="2" 
                      placeholder="Stratégie, émotions, plan..."
                      value={newTrade.notes}
                      onChange={(e) => setNewTrade({...newTrade, notes: e.target.value})}
                      className="w-full bg-[#0d0f17] border border-slate-700/80 rounded-xl px-3.5 py-2 text-sm text-white focus:outline-none focus:border-cyan-500"
                    ></textarea>
                  </div>

                  <button 
                    type="submit" 
                    className="w-full bg-gradient-to-r from-cyan-500 to-blue-600 hover:from-cyan-400 hover:to-blue-500 text-white font-medium py-2.5 rounded-xl transition-all duration-200 shadow-lg shadow-cyan-500/20"
                  >
                    Enregistrer le Trade
                  </button>
                </form>
              </div>

              {/* Table des trades */}
              <div className="lg:col-span-2 bg-[#131622] p-5 rounded-2xl border border-slate-800/80 shadow-xl overflow-hidden flex flex-col justify-between">
                <div>
                  <h3 className="text-lg font-semibold text-white mb-4">Historique des Exécutions</h3>
                  <div className="overflow-x-auto">
                    <table className="w-full text-left text-sm">
                      <thead className="bg-[#0d0f17] text-slate-400 text-xs uppercase">
                        <tr>
                          <th className="p-3">Actif</th>
                          <th className="p-3">Type</th>
                          <th className="p-3">Entrée / Sortie</th>
                          <th className="p-3">PnL ($)</th>
                          <th className="p-3">Remarques</th>
                          <th className="p-3 text-right">Action</th>
                        </tr>
                      </thead>
                      <tbody className="divide-y divide-slate-800/60">
                        {trades.map((t) => (
                          <tr key={t.id} className="hover:bg-slate-800/30 transition-colors">
                            <td className="p-3 font-semibold text-white">{t.pair}</td>
                            <td className="p-3">
                              <span className={`px-2 py-1 rounded-md text-[10px] font-bold ${
                                t.type === 'BUY' ? 'bg-emerald-500/10 text-emerald-400 border border-emerald-500/20' : 'bg-rose-500/10 text-rose-400 border border-rose-500/20'
                              }`}>
                                {t.type}
                              </span>
                            </td>
                            <td className="p-3 text-slate-300 text-xs">
                              {t.entry} → {t.exit}
                            </td>
                            <td className={`p-3 font-bold ${t.pnl >= 0 ? 'text-emerald-400' : 'text-rose-400'}`}>
                              {t.pnl >= 0 ? `+${t.pnl}` : t.pnl}$
                            </td>
                            <td className="p-3 text-slate-400 text-xs max-w-[150px] truncate">{t.notes || '-'}</td>
                            <td className="p-3 text-right">
                              <button 
                                onClick={() => handleDeleteTrade(t.id)}
                                className="text-slate-500 hover:text-rose-400 transition-colors"
                              >
                                <Trash2 size={16} />
                              </button>
                            </td>
                          </tr>
                        ))}
                      </tbody>
                    </table>
                  </div>
                </div>
              </div>

            </div>
          </div>
        )}

        {/* TAB 2 : PLAN 90 JOURS */}
        {activeTab === 'plan90' && (
          <div className="max-w-7xl mx-auto space-y-6">
            <div className="flex flex-col md:flex-row md:items-center justify-between gap-4">
              <div>
                <h2 className="text-2xl lg:text-3xl font-extrabold text-white flex items-center gap-3">
                  <TrendingUp className="text-cyan-400" /> Plan 10 USD - 90 Jours
                </h2>
                <p className="text-slate-400 text-sm mt-1">Génération d'intérêts composés quotidiens à un taux fixe de 6%.</p>
              </div>
              <div className="bg-gradient-to-r from-emerald-500/10 to-cyan-500/10 border border-emerald-500/30 px-4 py-2 rounded-xl text-right">
                <span className="text-xs text-slate-400 block">Capital Final estimé (Jour 90)</span>
                <span className="text-xl font-black text-emerald-400">1 894.64 $</span>
              </div>
            </div>

            {/* Graphique de Croissance */}
            <div className="bg-[#131622] p-5 rounded-2xl border border-slate-800/80 shadow-xl">
              <h3 className="text-base font-semibold text-white mb-4">Courbe de Croissance Cumulée</h3>
              <div className="h-64 w-full">
                <ResponsiveContainer width="100%" height="100%">
                  <AreaChart data={full90DaysData}>
                    <defs>
                      <linearGradient id="colorTotal" x1="0" y1="0" x2="0" y2="1">
                        <stop offset="5%" stopColor="#06b6d4" stopOpacity={0.4}/>
                        <stop offset="95%" stopColor="#06b6d4" stopOpacity={0}/>
                      </linearGradient>
                    </defs>
                    <CartesianGrid strokeDasharray="3 3" stroke="#1e293b" />
                    <XAxis dataKey="day" stroke="#64748b" tickLine={false} />
                    <YAxis stroke="#64748b" tickLine={false} />
                    <Tooltip 
                      contentStyle={{ backgroundColor: '#0d0f17', borderColor: '#334155', borderRadius: '12px' }}
                      labelStyle={{ color: '#94a3b8' }}
                    />
                    <Area type="monotone" dataKey="total" stroke="#06b6d4" strokeWidth={2} fillOpacity={1} fill="url(#colorTotal)" />
                  </AreaChart>
                </ResponsiveContainer>
              </div>
            </div>

            {/* Tableau complet */}
            <div className="bg-[#131622] p-5 rounded-2xl border border-slate-800/80 shadow-xl overflow-hidden">
              <h3 className="text-base font-semibold text-white mb-4">Tableau d'Évolution (Aperçu Clé)</h3>
              <div className="overflow-x-auto">
                <table className="w-full text-left text-sm">
                  <thead className="bg-[#0d0f17] text-slate-400 text-xs uppercase">
                    <tr>
                      <th className="p-3">Jour</th>
                      <th className="p-3">Capital Départ ($)</th>
                      <th className="p-3">Profit Quotidien (6%)</th>
                      <th className="p-3">Nouveau Total ($)</th>
                    </tr>
                  </thead>
                  <tbody className="divide-y divide-slate-800/60">
                    {baseData.map((row) => (
                      <tr key={row.day} className="hover:bg-slate-800/30 transition-colors">
                        <td className="p-3 font-medium text-cyan-400">Jour {row.day}</td>
                        <td className="p-3 text-slate-200">{row.funds.toFixed(2)} $</td>
                        <td className="p-3 text-emerald-400 font-semibold">+{row.profit.toFixed(2)} $</td>
                        <td className="p-3 text-white font-bold">{row.total.toFixed(2)} $</td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}

        {/* TAB 3 : CALCULATEUR SUR-MESURE */}
        {activeTab === 'calculator' && (
          <div className="max-w-7xl mx-auto space-y-6">
            <div>
              <h2 className="text-2xl lg:text-3xl font-extrabold text-white flex items-center gap-3">
                <Calculator className="text-cyan-400" /> Calculateur d'Intérêts Composés
              </h2>
              <p className="text-slate-400 text-sm mt-1">Simulez votre propre plan d'investissement personnalisé.</p>
            </div>

            <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
              {/* Inputs */}
              <div className="bg-[#131622] p-5 rounded-2xl border border-slate-800 shadow-xl space-y-4">
                <div>
                  <label className="text-xs text-slate-400 block mb-1">Capital Initial ($)</label>
                  <input 
                    type="number" 
                    value={customCapital}
                    onChange={(e) => setCustomCapital(e.target.value)}
                    className="w-full bg-[#0d0f17] border border-slate-700 rounded-xl p-3 text-white focus:outline-none focus:border-cyan-500"
                  />
                </div>
                <div>
                  <label className="text-xs text-slate-400 block mb-1">Taux Quotidien (%)</label>
                  <input 
                    type="number" 
                    value={customRate}
                    onChange={(e) => setCustomRate(e.target.value)}
                    className="w-full bg-[#0d0f17] border border-slate-700 rounded-xl p-3 text-white focus:outline-none focus:border-cyan-500"
                  />
                </div>
                <div>
                  <label className="text-xs text-slate-400 block mb-1">Durée (Jours)</label>
                  <input 
                    type="number" 
                    value={customDays}
                    onChange={(e) => setCustomDays(e.target.value)}
                    className="w-full bg-[#0d0f17] border border-slate-700 rounded-xl p-3 text-white focus:outline-none focus:border-cyan-500"
                  />
                </div>
              </div>

              {/* Résultats */}
              <div className="lg:col-span-2 bg-[#131622] p-5 rounded-2xl border border-slate-800 shadow-xl overflow-hidden">
                <h3 className="text-base font-semibold text-white mb-4">Résultats Simulés</h3>
                <div className="max-h-[400px] overflow-y-auto">
                  <table className="w-full text-left text-sm">
                    <thead className="bg-[#0d0f17] text-slate-400 text-xs uppercase sticky top-0">
                      <tr>
                        <th className="p-3">Jour</th>
                        <th className="p-3">Capital ($)</th>
                        <th className="p-3">Profit ($)</th>
                        <th className="p-3">Total ($)</th>
                      </tr>
                    </thead>
                    <tbody className="divide-y divide-slate-800/60">
                      {calculatedResults.map((item) => (
                        <tr key={item.day} className="hover:bg-slate-800/30">
                          <td className="p-3 text-cyan-400">Jour {item.day}</td>
                          <td className="p-3 text-slate-200">{item.funds} $</td>
                          <td className="p-3 text-emerald-400">+{item.profit} $</td>
                          <td className="p-3 text-white font-bold">{item.total} $</td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
        )}

      </main>

    </div>
  );
}
