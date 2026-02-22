<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <title>Ibkane IA</title>
    <link rel="manifest" href="/manifest.json" />
    <meta name="theme-color" content="#10b981" />
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
    <meta name="apple-mobile-web-app-title" content="Ibkane IA" />
    <link rel="apple-touch-icon" href="https://picsum.photos/seed/ibkane/180/180" />
    
    <!-- Google AdSense -->
    <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-YOUR_ID" crossorigin="anonymous"></script>
    
    <style>
      body { overscroll-behavior-y: contain; background-color: #09090b; }
    </style>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>{
  "name": "Ibkane IA",
  "short_name": "IbkaneIA",
  "description": "Assistant intelligent par Ibrahima Kane",
  "start_url": "/",
  "display": "standalone",
  "orientation": "portrait",
  "background_color": "#09090b",
  "theme_color": "#10b981",
  "icons": [
    {
      "src": "https://picsum.photos/seed/ibkane/192/192",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "https://picsum.photos/seed/ibkane/512/512",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ]
}import { GoogleGenAI } from "@google/genai";

const SYSTEM_INSTRUCTION = `Tu es Ibkane IA, un assistant intelligent créé par Ibrahima Kane, un élève de CPSF.
Ton nom vient de Ibrahima Kane.
Ibrahima Kane t'entraîne tous les jours.

Tu es un expert dans toutes les matières scolaires :
- Mathématiques (algèbre, géométrie, analyse, etc.)
- Physique-Chimie (PC)
- SVT (Sciences de la Vie et de la Terre)
- Français (dissertations, résumés, commentaires composés, grammaire, etc.)
- Arabe
- Et toutes les autres matières.

RÈGLE CRUCIALE POUR LES MATIÈRES SCIENTIFIQUES (Maths, PC, SVT) :
Lorsque l'utilisateur te donne un exercice de Mathématiques, de Physique-Chimie (PC) ou de SVT :
1. Présente la RÉSOLUTION complète étape par étape (calculs, formules, étapes logiques).
2. Termine par la SOLUTION FINALE (ex: S = {-3; 3}).
3. IMPORTANT : Ne donne AUCUNE EXPLICATION textuelle. Affiche uniquement les lignes de calcul.
4. Utilise un format clair, ligne par ligne.

Tu as accès à l'historique de la conversation pour te souvenir des messages précédents.

Règles d'identité :
1. Si on te demande "Qui t'a créé ?", tu dois répondre : "J'ai été créé par Ibrahima Kane un élève de CPSF et mon Nom vient de Ibrahima Kane."
2. Si on te demande "Qui t'entraîne ?", tu dois répondre : "Ibrahima Kane m'entraîne tous les jours."`;

export async function generateResponse(prompt: string, imageBase64?: string, history: { role: 'user' | 'assistant', content: string }[] = []) {
  const apiKey = import.meta.env.VITE_GEMINI_API_KEY || process.env.GEMINI_API_KEY;
  
  if (!apiKey) {
    return "Erreur : Clé API non configurée. Veuillez ajouter VITE_GEMINI_API_KEY dans les variables d'environnement de Vercel.";
  }

  const ai = new GoogleGenAI({ apiKey });
  const model = "gemini-3-flash-preview"; 
  
  const contents: any[] = [];
  for (const msg of history) {
    contents.push({
      role: msg.role === 'user' ? 'user' : 'model',
      parts: [{ text: msg.content }]
    });
  }

  const currentParts: any[] = [];
  if (imageBase64) {
    currentParts.push({
      inlineData: {
        mimeType: "image/jpeg",
        data: imageBase64.split(",")[1] || imageBase64,
      },
    });
  }
  currentParts.push({ text: prompt || "Analyse cette image et aide-moi avec l'exercice." });
  contents.push({ role: 'user', parts: currentParts });

  try {
    const response = await ai.models.generateContent({
      model,
      contents,
      config: { systemInstruction: SYSTEM_INSTRUCTION },
    });
    return response.text || "Désolé, je n'ai pas pu générer de réponse.";
  } catch (error) {
    console.error("Gemini API Error:", error);
    return "Une erreur est survenue lors de la communication avec l'IA.";
  }
}import tailwindcss from '@tailwindcss/vite';
import react from '@vitejs/plugin-react';
import path from 'path';
import {defineConfig} from 'vite';

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});{
  "name": "ibkane-ia",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "tsx server.ts",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "tsc --noEmit"
  },
  "dependencies": {
    "@google/genai": "^1.29.0",
    "@tailwindcss/vite": "^4.1.14",
    "@vitejs/plugin-react": "^5.0.4",
    "clsx": "^2.1.1",
    "dotenv": "^17.2.3",
    "express": "^4.21.2",
    "lucide-react": "^0.546.0",
    "motion": "^12.23.24",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "react-markdown": "^9.0.1",
    "tailwind-merge": "^3.0.1",
    "vite": "^6.2.0"
  },
  "devDependencies": {
    "@types/express": "^4.17.21",
    "@types/node": "^22.14.0",
    "autoprefixer": "^10.4.21",
    "tailwindcss": "^4.1.14",
    "tsx": "^4.21.0",
    "typescript": "~5.8.2"
  }
}
