# Frontend Interface Codebase Review

This document contains the complete React interface components for the application, organized by feature area. You can put this in your review document to showcase the UI implementation.

## Main Application Entry

### [App.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/App.jsx)
```jsx
// src/App.jsx
import React, { useEffect } from 'react';
import Layout from './components/Layout/Layout';
import ImageUpload from './components/Upload/ImageUpload';
import ChatInterface from './components/Chat/ChatInterface';
import { ArrowDown, Activity, ShieldCheck, Zap } from 'lucide-react';

import useAppStore from './store/useAppStore';
import ImageUpscale from './components/Upscale/ImageUpscale';
import Login from './components/Auth/Login';
import History from './components/History/History';
import { supabase } from './services/supabase';

function App() {
  const { currentView, isChatEnabled, user, setUser, setSession } = useAppStore();

  useEffect(() => {
    supabase.auth.getSession().then(({ data: { session } }) => {
      setSession(session);
      setUser(session?.user ?? null);
    });

    const {
      data: { subscription },
    } = supabase.auth.onAuthStateChange((_event, session) => {
      setSession(session);
      setUser(session?.user ?? null);
    });

    return () => subscription.unsubscribe();
  }, [setSession, setUser]);

  const scrollToAnalysis = () => {
    document.getElementById('analysis-section')?.scrollIntoView({ behavior: 'smooth' });
  };

  if (!user) {
    return <Login />;
  }

  return (
    <Layout>
      {currentView === 'home' && (
        <div className="flex flex-col gap-12 pb-10">
          {/* Hero Section */}
          <div className="min-h-[80vh] flex flex-col items-center justify-center text-center space-y-8">
            <div className="inline-flex items-center gap-2 px-4 py-2 rounded-full bg-teal-100 text-teal-700 font-medium text-sm animate-fade-in-up">
              <Activity size={16} />
              <span>AI-Powered Health Analysis</span>
            </div>

            <h1 className="text-5xl md:text-7xl font-bold text-slate-800 tracking-tight max-w-4xl leading-tight">
              Detect Vitamin Deficiencies with <span className="text-transparent bg-clip-text bg-gradient-to-r from-teal-500 to-cyan-600">AI Precision</span>
            </h1>

            <p className="text-xl text-slate-600 max-w-2xl leading-relaxed">
              Upload a photo and let Dr. Octopus analyze physical signs of vitamin deficiencies. Get instant, medical-grade insights powered by advanced machine learning.
            </p>

            <div className="flex gap-4 pt-4">
              <button
                onClick={scrollToAnalysis}
                className="px-8 py-4 bg-teal-500 hover:bg-teal-600 text-white rounded-2xl font-bold text-lg shadow-lg shadow-teal-500/30 transition-all hover:scale-105 flex items-center gap-2"
              >
                Start Analysis <ArrowDown size={20} />
              </button>
              <button className="px-8 py-4 bg-white hover:bg-slate-50 text-slate-700 border border-slate-200 rounded-2xl font-bold text-lg shadow-sm transition-all hover:scale-105">
                Learn More
              </button>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mt-16 w-full max-w-5xl">
              <FeatureCard
                icon={<Zap className="text-amber-500" />}
                title="Instant Results"
                desc="Get analysis in seconds using our advanced Colab model."
              />
              <FeatureCard
                icon={<ShieldCheck className="text-teal-500" />}
                title="Privacy First"
                desc="Your data is processed securely and never shared."
              />
              <FeatureCard
                icon={<Activity className="text-rose-500" />}
                title="Medical Insights"
                desc="Detailed explanations from our AI medical assistant."
              />
            </div>
          </div>

          {/* Analysis Section */}
          <div id="analysis-section" className="grid grid-cols-1 lg:grid-cols-12 gap-8 min-h-[85vh]">
            {/* Left Column: Image Upload */}
            <div className={`${isChatEnabled ? 'lg:col-span-7' : 'lg:col-span-12'} h-full flex flex-col transition-all duration-500 ease-in-out`}>
              <div className="flex-1 glass rounded-3xl p-6 shadow-xl border border-white/40 bg-white/20 backdrop-blur-md">
                <ImageUpload />
              </div>
            </div>

            {/* Right Column: Chat Interface */}
            {isChatEnabled && (
              <div className="lg:col-span-5 h-[650px] sticky top-8 transition-all duration-500 ease-in-out">
                <ChatInterface />
              </div>
            )}
          </div>
        </div>
      )}

      {currentView === 'upscale' && (
        <ImageUpscale />
      )}

      {currentView === 'dataset' && (
        <History />
      )}
    </Layout>
  );
}

const FeatureCard = ({ icon, title, desc }) => (
  <div className="p-6 rounded-2xl bg-white/60 backdrop-blur border border-white/50 shadow-sm hover:shadow-md transition-all text-left">
    <div className="w-12 h-12 rounded-xl bg-white shadow-sm flex items-center justify-center mb-4">
      {icon}
    </div>
    <h3 className="font-bold text-slate-800 text-lg mb-2">{title}</h3>
    <p className="text-slate-500 leading-relaxed">{desc}</p>
  </div>
);

export default App;
```

---

## Layout Components

### [Layout.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Layout/Layout.jsx)
```jsx
// src/components/Layout/Layout.jsx
import React from 'react';
import Sidebar from './Sidebar';
import Topbar from './Topbar';
import SettingsModal from '../Settings/SettingsModal';

const Layout = ({ children }) => {
    return (
        <div className="flex h-screen w-full overflow-hidden bg-[url('https://i.imgur.com/jJ0m0lF.jpeg')] bg-cover bg-center bg-no-repeat">
            {/* Overlay for better readability */}
            <div className="absolute inset-0 bg-white/60 backdrop-blur-[2px] z-0" />

            <div className="relative z-10 flex w-full h-full">
                <Sidebar />

                <main className="flex-1 flex flex-col h-full">
                    <Topbar />
                    <div className="flex-1 p-8 overflow-y-auto scrollbar-thin scrollbar-thumb-teal-200 scrollbar-track-transparent">
                        {children}
                    </div>
                </main>
            </div>

            <SettingsModal />
        </div>
    );
};

export default Layout;
```

### [Sidebar.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Layout/Sidebar.jsx)
```jsx
// src/components/Layout/Sidebar.jsx
import { Home, Database, Settings, Image as ImageIcon } from 'lucide-react';
import useAppStore from '../../store/useAppStore';
import clsx from 'clsx';

const Sidebar = () => {
    const { toggleSettings, currentView, setCurrentView } = useAppStore();

    return (
        <aside className="w-20 h-full flex flex-col items-center py-8 glass border-r border-white/20">
            <div className="mb-10">
                <div className="w-10 h-10 bg-teal-500 rounded-full flex items-center justify-center shadow-lg shadow-teal-500/30">
                    <span className="text-white font-bold text-xl">N</span>
                </div>
            </div>

            <nav className="flex-1 flex flex-col gap-6 w-full items-center">
                <NavItem
                    icon={<Home size={24} />}
                    label="Home"
                    active={currentView === 'home'}
                    onClick={() => setCurrentView('home')}
                />
                <NavItem
                    icon={<ImageIcon size={24} />}
                    label="Image Upscale"
                    active={currentView === 'upscale'}
                    onClick={() => setCurrentView('upscale')}
                />
                <NavItem
                    icon={<Database size={24} />}
                    label="Dataset"
                    active={currentView === 'dataset'}
                    onClick={() => setCurrentView('dataset')}
                />
            </nav>

            <div className="mt-auto">
                <button
                    onClick={toggleSettings}
                    className="p-3 rounded-xl hover:bg-white/20 transition-all duration-300 text-slate-600 hover:text-teal-600"
                    title="Settings"
                >
                    <Settings size={24} />
                </button>
            </div>
        </aside>
    );
};

const NavItem = ({ icon, label, active, onClick }) => (
    <button
        onClick={onClick}
        className={clsx(
            "p-3 rounded-xl transition-all duration-300 relative group",
            active ? "bg-teal-500 text-white shadow-lg shadow-teal-500/30" : "text-slate-500 hover:bg-white/20 hover:text-teal-600"
        )}
        title={label}
    >
        {icon}
        {active && (
            <div className="absolute -right-1 top-1/2 -translate-y-1/2 w-1 h-8 bg-teal-600 rounded-l-full" />
        )}
    </button>
);

export default Sidebar;
```

### [Topbar.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Layout/Topbar.jsx)
```jsx
// src/components/Layout/Topbar.jsx
import React, { useState } from 'react';
import { User, LogOut } from 'lucide-react';
import { motion, AnimatePresence } from 'framer-motion';
import useAppStore from '../../store/useAppStore';

const Topbar = () => {
    const [isProfileOpen, setIsProfileOpen] = useState(false);
    const { user, logout } = useAppStore();

    return (
        <header className="h-20 w-full flex items-center justify-between px-8">
            <h1 className="text-2xl font-bold text-slate-800 tracking-tight">Home</h1>

            <div className="relative">
                <button
                    onClick={() => setIsProfileOpen(!isProfileOpen)}
                    className="w-10 h-10 rounded-full bg-white shadow-md flex items-center justify-center text-slate-600 hover:text-teal-600 transition-colors border border-slate-100 overflow-hidden"
                >
                    {user?.picture ? (
                        <img src={user.picture} alt="Profile" className="w-full h-full object-cover" />
                    ) : (
                        <User size={20} />
                    )}
                </button>

                <AnimatePresence>
                    {isProfileOpen && (
                        <motion.div
                            initial={{ opacity: 0, y: 10, scale: 0.95 }}
                            animate={{ opacity: 1, y: 0, scale: 1 }}
                            exit={{ opacity: 0, y: 10, scale: 0.95 }}
                            className="absolute right-0 top-14 w-64 glass rounded-2xl shadow-xl p-4 z-50 border border-white/40"
                        >
                            <div className="flex items-center gap-3 mb-3">
                                <div className="w-12 h-12 rounded-full bg-teal-100 flex items-center justify-center text-teal-600 overflow-hidden">
                                    {user?.picture ? (
                                        <img src={user.picture} alt="Profile" className="w-full h-full object-cover" />
                                    ) : (
                                        <User size={24} />
                                    )}
                                </div>
                                <div className="overflow-hidden">
                                    <h3 className="font-semibold text-slate-800 truncate">{user?.name || 'User Profile'}</h3>
                                    <p className="text-xs text-slate-500 truncate">{user?.email || 'user@example.com'}</p>
                                </div>
                            </div>
                            <div className="h-px bg-slate-200 my-2" />
                            <button
                                onClick={() => {
                                    logout();
                                    setIsProfileOpen(false);
                                }}
                                className="w-full flex items-center gap-2 text-left px-3 py-2 rounded-lg hover:bg-red-50 text-red-500 text-sm transition-colors font-medium"
                            >
                                <LogOut size={16} />
                                Sign Out
                            </button>
                        </motion.div>
                    )}
                </AnimatePresence>
            </div>
        </header>
    );
};

export default Topbar;
```

---

## Core Feature Components

### [ImageUpload.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Upload/ImageUpload.jsx)
```jsx
// src/components/Upload/ImageUpload.jsx
import React, { useState, useRef } from 'react';
import { motion } from 'framer-motion';
import { Upload, X, Loader2, ScanLine } from 'lucide-react';
import clsx from 'clsx';
import useAppStore from '../../store/useAppStore';
import { predictImage } from '../../services/api';
import ResultDisplay from './ResultDisplay';

const ImageUpload = () => {
    const [dragActive, setDragActive] = useState(false);
    const [preview, setPreview] = useState(null);
    const [file, setFile] = useState(null);
    const inputRef = useRef(null);

    const {
        isAnalyzing,
        setIsAnalyzing,
        setAnalysisResult,
        analysisResult
    } = useAppStore();

    const handleDrag = (e) => {
        e.preventDefault();
        e.stopPropagation();
        if (e.type === "dragenter" || e.type === "dragover") {
            setDragActive(true);
        } else if (e.type === "dragleave") {
            setDragActive(false);
        }
    };

    const handleDrop = (e) => {
        e.preventDefault();
        e.stopPropagation();
        setDragActive(false);
        if (e.dataTransfer.files && e.dataTransfer.files[0]) {
            handleFile(e.dataTransfer.files[0]);
        }
    };

    const handleChange = (e) => {
        e.preventDefault();
        if (e.target.files && e.target.files[0]) {
            handleFile(e.target.files[0]);
        }
    };

    const handleFile = (file) => {
        setFile(file);
        const reader = new FileReader();
        reader.onloadend = () => {
            setPreview(reader.result);
        };
        reader.readAsDataURL(file);
        setAnalysisResult(null); // Reset previous result
    };

    const clearImage = () => {
        setFile(null);
        setPreview(null);
        setAnalysisResult(null);
        if (inputRef.current) {
            inputRef.current.value = '';
        }
    };

    const handleAnalyze = async () => {
        if (!file) return;

        setIsAnalyzing(true);
        try {
            const data = await predictImage(file);
            setAnalysisResult(data.predictions);
        } catch (error) {
            alert(error.message);
        } finally {
            setIsAnalyzing(false);
        }
    };

    return (
        <div className="h-full flex flex-col">
            <div
                className={clsx(
                    "relative flex-1 rounded-3xl border-2 border-dashed transition-all duration-300 flex flex-col items-center justify-center overflow-hidden group min-h-[300px]",
                    dragActive ? "border-teal-500 bg-teal-50/50" : "border-slate-300 hover:border-teal-400 bg-white/30",
                    preview ? "border-transparent" : ""
                )}
                onDragEnter={handleDrag}
                onDragLeave={handleDrag}
                onDragOver={handleDrag}
                onDrop={handleDrop}
            >
                <input
                    ref={inputRef}
                    type="file"
                    className="hidden"
                    onChange={handleChange}
                    accept="image/*"
                />

                {preview ? (
                    <div className="relative w-full h-full flex items-center justify-center p-4">
                        <img
                            src={preview}
                            alt="Preview"
                            className="max-w-full max-h-full object-contain rounded-xl shadow-lg"
                        />
                        <button
                            onClick={clearImage}
                            className="absolute top-4 right-4 p-2 bg-white/80 backdrop-blur rounded-full shadow-md hover:bg-red-50 text-slate-600 hover:text-red-500 transition-colors"
                        >
                            <X size={20} />
                        </button>
                        {isAnalyzing && (
                            <motion.div
                                className="absolute inset-0 bg-teal-500/10 z-10"
                                initial={{ opacity: 0 }}
                                animate={{ opacity: 1 }}
                            >
                                <motion.div
                                    className="w-full h-1 bg-teal-400 shadow-[0_0_15px_rgba(45,212,191,0.8)]"
                                    animate={{ top: ["0%", "100%", "0%"] }}
                                    transition={{ duration: 2, repeat: Infinity, ease: "linear" }}
                                    style={{ position: 'absolute' }}
                                />
                            </motion.div>
                        )}
                    </div>
                ) : (
                    <div className="text-center p-8">
                        <div
                            onClick={() => inputRef.current?.click()}
                            className="w-20 h-20 mx-auto mb-4 rounded-full bg-teal-50 flex items-center justify-center text-teal-500 cursor-pointer hover:scale-105 transition-transform shadow-sm"
                        >
                            <Upload size={32} />
                        </div>
                        <h3 className="text-xl font-semibold text-slate-700 mb-2">Upload Image</h3>
                        <p className="text-slate-500 mb-6 max-w-xs mx-auto">
                            Drag & drop your image here, or click to browse files
                        </p>
                        <button
                            onClick={() => inputRef.current?.click()}
                            className="px-6 py-2 rounded-xl bg-white border border-slate-200 text-slate-600 hover:border-teal-500 hover:text-teal-600 transition-all font-medium shadow-sm"
                        >
                            Select File
                        </button>
                    </div>
                )}
            </div>

            <div className="mt-6">
                <button
                    onClick={handleAnalyze}
                    disabled={!file || isAnalyzing}
                    className={clsx(
                        "w-full py-4 rounded-2xl font-bold text-lg shadow-lg transition-all flex items-center justify-center gap-3",
                        !file || isAnalyzing
                            ? "bg-slate-200 text-slate-400 cursor-not-allowed"
                            : "bg-gradient-to-r from-teal-500 to-cyan-500 text-white hover:shadow-teal-500/40 hover:scale-[1.02] active:scale-[0.98]"
                    )}
                >
                    {isAnalyzing ? (
                        <>
                            <Loader2 size={24} className="animate-spin" />
                            Analyzing...
                        </>
                    ) : (
                        <>
                            <ScanLine size={24} />
                            Analyze Vitamin Deficiency
                        </>
                    )}
                </button>
            </div>

            <ResultDisplay result={analysisResult} />
        </div>
    );
};

export default ImageUpload;
```

### [ResultDisplay.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Upload/ResultDisplay.jsx)
```jsx
// src/components/Upload/ResultDisplay.jsx
import React from 'react';
import { motion } from 'framer-motion';
import { Activity, Info } from 'lucide-react';

const ResultDisplay = ({ result }) => {
    if (!result) return null;

    const predictions = Array.isArray(result) ? result : [result];

    return (
        <motion.div
            initial={{ opacity: 0, y: 20 }}
            animate={{ opacity: 1, y: 0 }}
            className="mt-6 bg-white/80 backdrop-blur-md rounded-2xl p-6 border border-teal-100 shadow-xl max-h-[500px] overflow-y-auto scrollbar-thin scrollbar-thumb-teal-200 scrollbar-track-transparent"
        >
            <div className="flex items-center gap-3 mb-4 border-b border-teal-50 pb-3">
                <div className="w-10 h-10 rounded-full bg-teal-100 flex items-center justify-center text-teal-600">
                    <Activity size={20} />
                </div>
                <div>
                    <h3 className="text-lg font-bold text-slate-800">Analysis Report</h3>
                    <p className="text-xs text-slate-500">Model Predictions</p>
                </div>
            </div>

            <div className="space-y-6">
                {predictions.map((item, index) => (
                    <div key={index} className="bg-slate-50 rounded-xl p-6 text-slate-700 border border-slate-100 space-y-4">
                        <div className="flex justify-between items-center mb-4">
                            <h4 className="text-md font-bold text-teal-700 uppercase tracking-wider">
                                Prediction {index + 1}
                            </h4>
                            <span className="px-3 py-1 bg-teal-100 text-teal-800 rounded-full text-xs font-semibold">
                                {item.confidence}% Match
                            </span>
                        </div>

                        {item.image_url && (
                            <div className="mb-4 flex justify-center">
                                <img
                                    src={item.image_url}
                                    alt="Uploaded"
                                    style={{ width: "200px", borderRadius: "10px" }}
                                    className="shadow-md border border-slate-200"
                                />
                            </div>
                        )}

                        <dl className="grid grid-cols-1 gap-4">
                            <div className="bg-white p-4 rounded-lg shadow-sm border border-slate-100 hover:border-teal-200 transition-colors">
                                <dt className="flex items-center gap-2 text-xs font-bold text-teal-600 uppercase tracking-wider mb-1">
                                    <div className="w-2 h-2 rounded-full bg-teal-400" />
                                    Vitamin
                                </dt>
                                <dd className="text-lg font-semibold text-slate-800 break-words pl-4">
                                    {item.vitamin || 'Unknown'}
                                </dd>
                            </div>

                            <div className="bg-white p-4 rounded-lg shadow-sm border border-slate-100 hover:border-teal-200 transition-colors">
                                <dt className="flex items-center gap-2 text-xs font-bold text-teal-600 uppercase tracking-wider mb-1">
                                    <div className="w-2 h-2 rounded-full bg-teal-400" />
                                    Common Symptoms
                                </dt>
                                <dd className="text-md font-medium text-slate-700 break-words pl-4">
                                    <ul className="list-disc pl-5 mt-1 space-y-1">
                                        {item.symptoms && Array.isArray(item.symptoms) ? (
                                            item.symptoms.map((symptom, i) => (
                                                <li key={i}>{symptom}</li>
                                            ))
                                        ) : (
                                            <li>{item.symptoms || 'None'}</li>
                                        )}
                                    </ul>
                                </dd>
                            </div>

                            <div className="bg-white p-4 rounded-lg shadow-sm border border-slate-100 hover:border-teal-200 transition-colors">
                                <dt className="flex items-center gap-2 text-xs font-bold text-teal-600 uppercase tracking-wider mb-1">
                                    <div className="w-2 h-2 rounded-full bg-teal-400" />
                                    Recommended Foods
                                </dt>
                                <dd className="text-md font-medium text-slate-700 break-words pl-4">
                                    {item.recommended_foods && Array.isArray(item.recommended_foods) ? item.recommended_foods.join(", ") : (item.recommended_foods || 'None')}
                                </dd>
                            </div>
                        </dl>
                    </div>
                ))}

                <div className="flex items-start gap-2 text-xs text-slate-400 bg-blue-50/50 p-3 rounded-lg mt-4">
                    <Info size={14} className="mt-0.5 flex-shrink-0 text-blue-400" />
                    <p>
                        This is a raw prediction from the AI model. Please verify with a healthcare professional.
                    </p>
                </div>
            </div>
        </motion.div>
    );
};

export default ResultDisplay;
```

### [ChatInterface.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Chat/ChatInterface.jsx)
```jsx
// src/components/Chat/ChatInterface.jsx
import React, { useEffect, useRef, useState } from 'react';
import { motion } from 'framer-motion';
import { Send, Bot, User, Sparkles, Square } from 'lucide-react';
import useAppStore from '../../store/useAppStore';
import { generateGeminiResponse, generatePatientExplanation } from '../../services/gemini';
import clsx from 'clsx';
import ReactMarkdown from 'react-markdown';

const ChatInterface = () => {
    const {
        chatHistory,
        addChatMessage,
        analysisResult,
        isChatEnabled,
        lastExplainedResult,
        setLastExplainedResult
    } = useAppStore();

    const [input, setInput] = useState('');
    const [isTyping, setIsTyping] = useState(false);
    const messagesEndRef = useRef(null);
    const abortControllerRef = useRef(null);

    useEffect(() => {
        if (isChatEnabled && analysisResult && analysisResult !== lastExplainedResult) {
            setLastExplainedResult(analysisResult);

            const autoExplain = async () => {
                abortControllerRef.current = new AbortController();
                setIsTyping(true);
                try {
                    const response = await generatePatientExplanation(analysisResult, abortControllerRef.current.signal);
                    addChatMessage({ role: 'model', content: response });
                } catch (error) {
                    if (error.name !== 'CanceledError') {
                        addChatMessage({ role: 'model', content: "Error generating explanation: " + error.message });
                    }
                } finally {
                    setIsTyping(false);
                }
            };
            autoExplain();
        }
    }, [analysisResult, isChatEnabled, addChatMessage, lastExplainedResult, setLastExplainedResult]);

    const scrollToBottom = () => {
        messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
    };

    useEffect(() => {
        scrollToBottom();
    }, [chatHistory, isTyping]);

    const handleSend = async (e) => {
        e.preventDefault();
        if (!input.trim() || isTyping) return;

        const userMsg = { role: 'user', content: input };
        addChatMessage(userMsg);
        setInput('');
        setIsTyping(true);

        abortControllerRef.current = new AbortController();
        try {
            const context = isChatEnabled ? analysisResult : null;
            const response = await generateGeminiResponse(chatHistory, input, context, abortControllerRef.current.signal);
            addChatMessage({ role: 'model', content: response });
        } catch (error) {
            if (error.name !== 'CanceledError') {
                addChatMessage({ role: 'model', content: "Error: " + error.message });
            }
        } finally {
            setIsTyping(false);
        }
    };

    const handleStop = () => {
        if (abortControllerRef.current) {
            abortControllerRef.current.abort();
        }
    };

    return (
        <div className="flex flex-col h-full bg-white/40 glass rounded-3xl overflow-hidden border border-white/40 shadow-xl">
            <div className="p-4 border-b border-white/20 bg-white/20 backdrop-blur-md flex items-center gap-3 shrink-0">
                <div className="w-10 h-10 rounded-full bg-gradient-to-br from-teal-400 to-cyan-500 flex items-center justify-center text-white shadow-lg shadow-teal-500/20">
                    <Bot size={20} />
                </div>
                <div>
                    <h3 className="font-bold text-slate-800">Dr. Octopus</h3>
                    <p className="text-xs text-slate-500 flex items-center gap-1">
                        <span className="w-2 h-2 rounded-full bg-green-500 animate-pulse" />
                        Online Assistant
                    </p>
                </div>
            </div>

            <div className="flex-1 overflow-y-auto p-4 space-y-4 scrollbar-thin scrollbar-thumb-slate-200 scrollbar-track-transparent">
                {chatHistory.length === 0 && (
                    <div className="h-full flex flex-col items-center justify-center text-slate-400 opacity-60">
                        <Sparkles size={48} className="mb-4 text-teal-300" />
                        <p className="text-center text-sm">Hello! I'm Dr. Octopus.<br />How can I help you today?</p>
                    </div>
                )}

                {chatHistory.map((msg, idx) => (
                    <motion.div
                        key={idx}
                        initial={{ opacity: 0, y: 10 }}
                        animate={{ opacity: 1, y: 0 }}
                        className={clsx(
                            "flex gap-3 max-w-[90%]",
                            msg.role === 'user' ? "ml-auto flex-row-reverse" : ""
                        )}
                    >
                        <div className={clsx(
                            "w-8 h-8 rounded-full flex items-center justify-center flex-shrink-0 shadow-sm",
                            msg.role === 'user' ? "bg-slate-200 text-slate-600" : "bg-teal-100 text-teal-600"
                        )}>
                            {msg.role === 'user' ? <User size={16} /> : <Bot size={16} />}
                        </div>
                        <div className={clsx(
                            "p-3 rounded-2xl text-sm leading-relaxed shadow-sm",
                            msg.role === 'user'
                                ? "bg-slate-800 text-white rounded-tr-none whitespace-pre-wrap"
                                : "bg-white text-slate-700 rounded-tl-none border border-slate-100"
                        )}>
                            {msg.role === 'user' ? (
                                msg.content
                            ) : (
                                <div className="markdown-content space-y-2">
                                    <ReactMarkdown
                                        components={{
                                            p: ({ node, ...props }) => <p className="mb-2 last:mb-0" {...props} />,
                                            ul: ({ node, ...props }) => <ul className="list-disc pl-5 mb-2" {...props} />,
                                            ol: ({ node, ...props }) => <ol className="list-decimal pl-5 mb-2" {...props} />,
                                            li: ({ node, ...props }) => <li className="mb-1" {...props} />,
                                            h1: ({ node, ...props }) => <h1 className="text-lg font-bold mb-2" {...props} />,
                                            h2: ({ node, ...props }) => <h2 className="text-md font-bold mb-2" {...props} />,
                                            h3: ({ node, ...props }) => <h3 className="text-sm font-bold mb-2" {...props} />,
                                            strong: ({ node, ...props }) => <strong className="font-bold text-teal-800" {...props} />,
                                        }}
                                    >
                                        {msg.content}
                                    </ReactMarkdown>
                                </div>
                            )}
                        </div>
                    </motion.div>
                ))}

                {isTyping && (
                    <motion.div
                        initial={{ opacity: 0 }}
                        animate={{ opacity: 1 }}
                        className="flex gap-3"
                    >
                        <div className="w-8 h-8 rounded-full bg-teal-100 text-teal-600 flex items-center justify-center flex-shrink-0">
                            <Bot size={16} />
                        </div>
                        <div className="bg-white p-4 rounded-2xl rounded-tl-none border border-slate-100 shadow-sm flex gap-1">
                            <span className="w-2 h-2 bg-slate-400 rounded-full animate-bounce" style={{ animationDelay: '0ms' }} />
                            <span className="w-2 h-2 bg-slate-400 rounded-full animate-bounce" style={{ animationDelay: '150ms' }} />
                            <span className="w-2 h-2 bg-slate-400 rounded-full animate-bounce" style={{ animationDelay: '300ms' }} />
                        </div>
                    </motion.div>
                )}
                <div ref={messagesEndRef} />
            </div>

            <form onSubmit={handleSend} className="p-4 bg-white/30 border-t border-white/20 backdrop-blur-sm shrink-0">
                <div className="relative">
                    <input
                        type="text"
                        value={input}
                        onChange={(e) => setInput(e.target.value)}
                        placeholder="Ask Dr. Octopus..."
                        className="w-full pl-4 pr-12 py-3 rounded-xl border border-slate-200 focus:outline-none focus:ring-2 focus:ring-teal-500/50 bg-white/80 shadow-inner"
                    />
                    {isTyping ? (
                        <button
                            type="button"
                            onClick={handleStop}
                            className="absolute right-2 top-1/2 -translate-y-1/2 p-2 bg-red-400 text-white rounded-lg hover:bg-red-500 transition-colors shadow-md shadow-red-500/20"
                        >
                            <Square size={18} fill="currentColor" />
                        </button>
                    ) : (
                        <button
                            type="submit"
                            disabled={!input.trim()}
                            className="absolute right-2 top-1/2 -translate-y-1/2 p-2 bg-teal-500 text-white rounded-lg hover:bg-teal-600 disabled:opacity-50 disabled:cursor-not-allowed transition-colors shadow-md shadow-teal-500/20"
                        >
                            <Send size={18} />
                        </button>
                    )}
                </div>
            </form>
        </div>
    );
};

export default ChatInterface;
```

---

## Authentication & Modals

### [Login.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Auth/Login.jsx)
```jsx
// src/components/Auth/Login.jsx
import { supabase } from '../../services/supabase';
import useAppStore from '../../store/useAppStore';
import { ShieldCheck, Activity } from 'lucide-react';

const Login = () => {
    const handleGoogleLogin = async () => {
        const { data, error } = await supabase.auth.signInWithOAuth({
            provider: 'google',
            options: {
                redirectTo: window.location.origin
            }
        });
        if (error) {
            console.error('Login Failed:', error.message);
        }
    };

    return (
        <div className="min-h-screen w-full flex items-center justify-center bg-[url('https://i.imgur.com/jJ0m0lF.jpeg')] bg-cover bg-center bg-no-repeat">
            <div className="absolute inset-0 bg-white/60 backdrop-blur-sm z-0" />

            <div className="relative z-10 w-full max-w-md p-8 glass rounded-3xl shadow-2xl border border-white/40 bg-white/40 text-center animate-fade-in-up">
                <div className="w-16 h-16 bg-teal-500 rounded-2xl flex items-center justify-center shadow-lg shadow-teal-500/30 mx-auto mb-6">
                    <Activity size={32} className="text-white" />
                </div>

                <h1 className="text-3xl font-bold text-slate-800 mb-2">Welcome Back</h1>
                <p className="text-slate-600 mb-8">Please sign in to access your secure health dashboard and analysis tools.</p>

                <div className="flex justify-center mb-8">
                    <button
                        onClick={handleGoogleLogin}
                        className="flex items-center gap-3 bg-white text-slate-700 hover:bg-slate-50 font-semibold py-3 px-8 rounded-full border border-slate-200 shadow-sm transition-all hover:shadow-md hover:scale-105"
                    >
                        Sign in with Google
                    </button>
                </div>

                <div className="mt-8 flex items-center justify-center gap-2 text-sm text-slate-500 bg-white/50 py-3 rounded-xl border border-slate-100">
                    <ShieldCheck size={16} className="text-teal-600" />
                    <span>Secure Access & Data Privacy</span>
                </div>
            </div>
        </div>
    );
};

export default Login;
```

### [SettingsModal.jsx](file:///c:/Users/DELL/Desktop/vitmin%20def%20interface/frontend/src/components/Settings/SettingsModal.jsx)
```jsx
// src/components/Settings/SettingsModal.jsx
import React, { useState, useEffect } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import { X, Save, Key, Database, MessageSquare } from 'lucide-react';
import useAppStore from '../../store/useAppStore';

const SettingsModal = () => {
    const {
        isSettingsOpen,
        toggleSettings,
        apiKey,
        setApiKey,
        colabEndpoint,
        setColabEndpoint,
        upscaleEndpoint,
        setUpscaleEndpoint,
        isChatEnabled,
        setChatEnabled
    } = useAppStore();

    const handleSave = () => {
        toggleSettings();
    };

    return (
        <AnimatePresence>
            {isSettingsOpen && (
                <div className="fixed inset-0 z-50 flex items-center justify-center p-4">
                    <motion.div
                        initial={{ opacity: 0 }}
                        animate={{ opacity: 1 }}
                        exit={{ opacity: 0 }}
                        onClick={toggleSettings}
                        className="absolute inset-0 bg-black/40 backdrop-blur-sm"
                    />

                    <motion.div
                        initial={{ scale: 0.9, opacity: 0, y: 20 }}
                        animate={{ scale: 1, opacity: 1, y: 0 }}
                        exit={{ scale: 0.9, opacity: 0, y: 20 }}
                        className="relative w-full max-w-md glass rounded-2xl p-6 shadow-2xl border border-white/40 bg-white/80"
                    >
                        <div className="flex items-center justify-between mb-6">
                            <h2 className="text-xl font-bold text-slate-800">Settings</h2>
                            <button
                                onClick={toggleSettings}
                                className="p-2 rounded-full hover:bg-slate-100 text-slate-500 transition-colors"
                            >
                                <X size={20} />
                            </button>
                        </div>

                        <div className="space-y-4">
                            <div>
                                <label className="block text-sm font-medium text-slate-700 mb-1 flex items-center gap-2">
                                    <Key size={16} className="text-teal-500" />
                                    Gemini API Key
                                </label>
                                <input
                                    type="password"
                                    value={apiKey}
                                    onChange={(e) => setApiKey(e.target.value)}
                                    placeholder="Enter your Gemini API Key"
                                    className="w-full px-4 py-2 rounded-xl border border-slate-200 focus:outline-none focus:ring-2 focus:ring-teal-500/50 bg-white/50"
                                />
                            </div>

                            {/* Additional settings omitted here for brevity (see earlier code) */}
                            
                        </div>

                        <div className="mt-8 flex justify-end">
                            <button
                                onClick={handleSave}
                                className="flex items-center gap-2 px-6 py-2 bg-teal-500 hover:bg-teal-600 text-white rounded-xl font-medium transition-colors shadow-lg shadow-teal-500/30"
                            >
                                <Save size={18} />
                                Save Settings
                            </button>
                        </div>
                    </motion.div>
                </div>
            )}
        </AnimatePresence>
    );
};

export default SettingsModal;
```
