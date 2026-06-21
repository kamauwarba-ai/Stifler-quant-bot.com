import fs from 'fs';
import { execSync } from 'child_process';

console.log('📱 INITIALIZING REACT NATIVE EXPO QUANT DASHBOARD MOBILE APP...');

// 1. Structural Layer Generation for Expo Native App
const directories = [
  'mobile',
  'mobile/components',
  'mobile/assets',
];
directories.forEach(dir => {
  if (!fs.existsSync(dir)) fs.mkdirSync(dir, { recursive: true });
});

// 2. Build Mobile Package Configurations & NativeWind Dependencies
const mobilePackageJson = {
  name: "solana-forex-arb-mobile",
  version: "1.0.0",
  scripts: {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web"
  },
  dependencies: {
    "expo": "~51.0.0",
    "expo-status-bar": "~1.12.1",
    "react": "18.2.0",
    "react-native": "0.74.1",
    "nativewind": "^2.0.11",
    "tailwindcss": "^3.3.2"
  },
  devDependencies: {
    "@babel/core": "^7.20.0"
  },
  private: true
};
fs.writeFileSync('mobile/package.json', JSON.stringify(mobilePackageJson, null, 2));

// 3. Write Mobile Configuration Files (Babel & Tailwind)
const babelConfig = `module.exports = function(api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ["nativewind/babel"],
  };
};`;
fs.writeFileSync('mobile/babel.config.js', babelConfig);

const tailwindConfig = `/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./App.{js,jsx,ts,tsx}", "./components/**/*.{js,jsx,ts,tsx}"],
  theme: { extend: {} },
  plugins: [],
}`;
fs.writeFileSync('mobile/tailwind.config.js', tailwindConfig);

// 4. Write the Core Cross-Market Mobile Code (App.js)
const appJs = `import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, ScrollView, TouchableOpacity, SafeAreaView, StatusBar, FlatList } from 'react-native';
import { StatusBar as ExpoStatusBar } from 'expo-status-bar';

// NOTE: When running on a real physical mobile device, replace 'localhost' 
// with your machine's local network IP address (e.g., '119.168.1.50')
const BACKEND_URL = 'http://localhost:5000/api';

export default function App() {
  const [cryptoPairs, setCryptoPairs] = useState([]);
  const [forexPairs, setForexPairs] = useState([]);
  const [logs, setLogs] = useState([]);
  const [loading, setLoading] = useState(false);

  const fetchMarkets = async () => {
    try {
      const res = await fetch(\`\${BACKEND_URL}/opportunities\`);
      const json = await res.json();
      if (json.success) {
        setCryptoPairs(json.solanaData || []);
        setForexPairs(json.forexData || []);
      }
    } catch (err) {
      // Internal structural logging if communication channel fails
    }
  };

  useEffect(() => {
    fetchMarkets();
    const timer = setInterval(fetchMarkets, 1000); // 1-second ticks for mobile network conservation
    return () => clearInterval(timer);
  }, []);

  const triggerSwap = async (id) => {
    setLoading(true);
    setLogs(prev => ["Constructing Mobile Jito Bundle...", ...prev]);
    try {
      const res = await fetch(\`\${BACKEND_URL}/execute\`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ opportunityId: id })
      });
      const json = await res.json();
      if (json.success) {
        setLogs(prev => [...json.executionLogs, ...prev]);
      }
    } catch (err) {
      setLogs(prev => ["Mobile execution tunnel route failed.", ...prev]);
    } finally {
      setLoading(false);
    }
  };

  return (
    <SafeAreaView className="flex-1 bg-slate-950 pt-8 text-white">
      <ExpoStatusBar style="light" />
      
      {/* Header panel component view layout */}
      <View className="p-4 border-b border-slate-800">
        <Text className="text-xl font-black text-emerald-400 tracking-wider">⚡ QUANT STATION MOBILE</Text>
        <Text className="text-xs text-slate-400">Cross-Market Solana & Foreign Exchange Engine</Text>
      </View>

      <ScrollView className="flex-1 p-4">
        {/* Section 1: Live On-Chain Solana Arb Opportunities */}
        <Text className="text-xs font-bold tracking-widest text-slate-400 uppercase mb-3">🚀 SOLANA LIQUIDITY ARB</Text>
        {cryptoPairs.map((pair) => (
          <View key={pair.id} className="bg-slate-900 border border-slate-800 rounded-xl p-4 mb-5">
            <View className="flex-row justify-between items-center mb-2">
              <Text className="text-base font-bold text-slate-100 font-mono">{pair.pair}</Text>
              <View className="bg-emerald-950 px-2 py-0.5 rounded border border-emerald-900">
                <Text className="text-emerald-400 text-xs font-bold">{pair.status}</Text>
              </View>
            </View>
            
            <View className="flex-row justify-between bg-black/40 p-3 rounded-lg border border-slate-800/40 mb-3">
              <View className="items-center flex-1"><Text className="text-[10px] text-slate-500">Buy</Text><Text className="text-xs text-slate-300 font-mono">\${pair.buyPrice}</Text></View>
              <View className="items-center flex-1"><Text className="text-[10px] text-slate-500">Sell</Text><Text className="text-xs text-slate-300 font-mono">\${pair.sellPrice}</Text></View>
              <View className="items-center flex-1"><Text className="text-[10px] text-emerald-500">Spread</Text><Text className="text-xs text-emerald-400 font-mono">+{pair.spreadPct}%</Text></View>
            </View>

            <View className="flex-row justify-between items-center">
              <View>
                <Text className="text-[10px] text-slate-500">Clean Yield Est.</Text>
                <Text className="text-lg font-black text-emerald-400 font-mono">\${pair.estProfitUsd}</Text>
              </View>
              <TouchableOpacity 
                onPress={() => triggerSwap(pair.id)} 
                disabled={loading}
                className="bg-emerald-500 px-4 py-2.5 rounded-lg active:bg-emerald-400"
              >
                <Text className="text-slate-950 text-xs font-bold tracking-wide">EXECUTE SWAP</Text>
              </TouchableOpacity>
            </View>
          </View>
        ))}

        {/* Section 2: Forex Monitoring Streams */}
        <Text className="text-xs font-bold tracking-widest text-slate-400 uppercase mb-3">💼 FOREX MARKET MONITOR</Text>
        <View className="bg-slate-900 border border-slate-800 rounded-xl overflow-hidden mb-6">
          {forexPairs.map((fx, i) => (
            <View key={i} className="flex-row justify-between items-center p-3 border-b border-slate-800/60 last:border-b-0">
              <View>
                <Text className="text-sm font-bold text-slate-200 font-mono">{fx.pair}</Text>
                <Text className="text-[10px] text-slate-500">{fx.volume}</Text>
              </View>
              <View className="items-end">
                <Text className="text-sm font-bold text-slate-100 font-mono">{fx.rate.toFixed(4)}</Text>
                <Text className={\`text-xs font-bold \${fx.change >= 0 ? 'text-emerald-400' : 'text-rose-400'}\`}>
                  {(fx.change >= 0 ? '+' : '') + fx.change.toFixed(2)}%
                </Text>
              </View>
            </View>
          ))}
        </View>

        {/* Section 3: Log Outputs */}
        <Text className="text-xs font-bold tracking-widest text-slate-400 uppercase mb-3">📋 ENGINE LOG BUFFER</Text>
        <View className="bg-black rounded-xl p-4 border border-slate-900 min-h-[160px] mb-8">
          {logs.length === 0 ? (
            <Text className="text-slate-600 text-xs italic">Awaiting transaction execution requests...</Text>
          ) : (
            logs.map((log, i) => (
              <Text key={i} className="text-slate-300 font-mono text-[11px] mb-1.5 leading-4">&gt; {log}</Text>
            ))
          )}
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}`;
fs.writeFileSync('mobile/App.js', appJs);

console.log('📦 Executing build installations for mobile modules...');
execSync('npm install', { cwd: 'mobile', stdio: 'inherit' });

console.log('✅ MOBILE WORKSPACE SCRIVENER
