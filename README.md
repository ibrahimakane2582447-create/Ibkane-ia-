{
  "name": "ibkane-ia",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "@google/genai": "^1.29.0",
    "lucide-react": "^0.475.0",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "react-markdown": "^9.0.1",
    "motion": "^12.23.24",
    "clsx": "^2.1.1",
    "tailwind-merge": "^3.0.1"
  },
  "devDependencies": {
    "@tailwindcss/vite": "^4.1.14",
    "@types/react": "^19.0.0",
    "@types/react-dom": "^19.0.0",
    "tailwindcss": "^4.1.14",
    "typescript": "~5.8.2",
    "vite": "^6.2.0",
    "@vitejs/plugin-react": "^5.0.4"
  }
}{
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}import { GoogleGenAI } from "@google/genai";

const SYSTEM_INSTRUCTION = `Tu es Ibkane IA, un assistant intelligent créé par Ibrahima Kane, un élève de CPSF.
Ton nom vient de Ibrahima Kane. Ibrahima Kane t'entraîne tous les jours.

RÈGLE CRUCIALE POUR LES MATIÈRES SCIENTIFIQUES (Maths, PC, SVT) :
1. Présente la RÉSOLUTION complète étape par étape (calculs, formules).
2. Termine par la SOLUTION FINALE (ex: S = {-3; 3}).
3. IMPORTANT : Ne donne AUCUNE EXPLICATION textuelle. Affiche uniquement les lignes de calcul.

Règles d'identité :
- Créateur : Ibrahima Kane, élève de CPSF.
- Entraîneur : Ibrahima Kane.`;

export async function generateResponse(prompt: string, imageBase64?: string, history: { role: 'user' | 'assistant', content: string }[] = []) {
  const apiKey = import.meta.env.VITE_GEMINI_API_KEY;
  
  if (!apiKey) {
    return "Erreur : Clé API non configurée sur Vercel (VITE_GEMINI_API_KEY).";
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
      inlineData: { mimeType: "image/jpeg", data: imageBase64.split(",")[1] || imageBase64 }
    });
  }
  currentParts.push({ text: prompt || "Aide-moi avec cet exercice." });
  contents.push({ role: 'user', parts: currentParts });

  try {
    const response = await ai.models.generateContent({
      model,
      contents,
      config: { systemInstruction: SYSTEM_INSTRUCTION },
    });
    return response.text || "Désolé, je n'ai pas pu répondre.";
  } catch (error) {
    return "Une erreur est survenue lors de la communication avec l'IA.";
  }
}{
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
}
