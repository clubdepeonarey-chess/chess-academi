import React, { useState, useEffect } from "react";

// Web de Ajedrez - Single-file React app (Tailwind CSS expected in the host project)
// Funcionalidades incluidas (prototipo):
// - Navegacion: Home / Lecciones / Subir / Entrenamientos
// - Listado de lecciones con filtros y vista de detalle
// - Formulario para "subir" contenido (simulado, guarda en localStorage)
// - Panel de entrenamientos: crear sesiones, unirse a sesiones (estado local)
// - Tablero de ajedrez interactivo minimal (solo para demostrar una posición y mover piezas de ejemplo)

// Nota: Este archivo es un punto de partida. Para producción necesitás conectar
// backend (autenticación, almacenamiento de archivos, WebSockets para entrenamientos en vivo, motor de juego, base de datos, etc.).

// --- Simple Chessboard (sin motor) ---
const initialPosition = {
  a2: "wp", b2: "wp", c2: "wp", d2: "wp", e2: "wp", f2: "wp", g2: "wp", h2: "wp",
  a7: "bp", b7: "bp", c7: "bp", d7: "bp", e7: "bp", f7: "bp", g7: "bp", h7: "bp",
  a1: "wr", h1: "wr", a8: "br", h8: "br",
  b1: "wn", g1: "wn", b8: "bn", g8: "bn",
  c1: "wb", f1: "wb", c8: "bb", f8: "bb",
  d1: "wq", e1: "wk", d8: "bq", e8: "bk",
};

function Chessboard({ position = initialPosition, onPositionChange }) {
  const [pos, setPos] = useState(position);
  const [selected, setSelected] = useState(null);

  useEffect(() => setPos(position), [position]);

  const files = ["a","b","c","d","e","f","g","h"];
  const ranks = [8,7,6,5,4,3,2,1];

  function handleSquareClick(file, rank) {
    const key = `${file}${rank}`;
    if (selected) {
      // move piece
      const newPos = { ...pos };
      newPos[key] = newPos[selected];
      delete newPos[selected];
      setPos(newPos);
      setSelected(null);
      onPositionChange && onPositionChange(newPos);
    } else if (pos[key]) {
      setSelected(key);
    }
  }

  function renderPiece(code) {
    if (!code) return null;
    const type = code[1];
    const color = code[0] === "w" ? "white" : "black";
    // simple unicode pieces
    const map = { k: "♚", q: "♛", r: "♜", b: "♝", n: "♞", p: "♟" };
    return (
      <div className="text-2xl select-none">{color === "white" ? map[type] : map[type]}</div>
    );
  }

  return (
    <div className="grid grid-cols-8 gap-0 border-2 border-gray-300 shadow-md w-[360px]">
      {ranks.map((rank) =>
        files.map((file, fi) => {
          const squareKey = `${file}${rank}`;
          const isDark = (fi + rank) % 2 === 0;
          const bg = isDark ? "bg-slate-700" : "bg-amber-100";
          const isSelected = selected === squareKey;
          return (
            <div
              key={squareKey}
              onClick={() => handleSquareClick(file, rank)}
              className={`${bg} w-9 h-9 flex items-center justify-center cursor-pointer ${isSelected ? "ring-2 ring-indigo-400" : ""}`}
              title={squareKey}
            >
              {renderPiece(pos[squareKey])}
            </div>
          );
        })
      )}
    </div>
  );
}

// --- Main App ---
export default function App() {
  const [tab, setTab] = useState("home");

  // Lessons (persist in localStorage)
  const [lessons, setLessons] = useState(() => {
    try {
      const raw = localStorage.getItem("chess_lessons_v1");
      return raw ? JSON.parse(raw) : sampleLessons();
    } catch (e) {
      return sampleLessons();
    }
  });

  useEffect(() => {
    localStorage.setItem("chess_lessons_v1", JSON.stringify(lessons));
  }, [lessons]);

  // Upload form
  const [uploadState, setUploadState] = useState({ title: "", description: "" });

  // Training sessions
  const [sessions, setSessions] = useState(() => {
    try {
      const raw = localStorage.getItem("chess_sessions_v1");
      return raw ? JSON.parse(raw) : [];
    } catch (e) {
      return [];
    }
  });

  useEffect(() => {
    localStorage.setItem("chess_sessions_v1", JSON.stringify(sessions));
  }, [sessions]);

  function addLesson(lesson) {
    setLessons((s) => [{ ...lesson, id: Date.now() }, ...s]);
  }

  function handleUpload(e) {
    e.preventDefault();
    if (!uploadState.title) return alert("Agregá un título");
    const newLesson = { id: Date.now(), title: uploadState.title, description: uploadState.description, createdAt: new Date().toISOString(), author: "Tú" };
    addLesson(newLesson);
    setUploadState({ title: "", description: "" });
    alert("Contenido subido (simulado). En producción se enviaría a un backend.");
  }

  function createSession({ title, datetime }) {
    const s = { id: Date.now(), title, datetime, players: [] };
    setSessions((x) => [s, ...x]);
  }

  function joinSession(id, playerName = "Invitado") {
    setSessions((all) => all.map(s => s.id === id ? { ...s, players: Array.from(new Set([...s.players, playerName])) } : s));
  }

  return (
    <div className="min-h-screen bg-gradient-to-b from-white to-slate-50 p-6">
      <div className="max-w-6xl mx-auto">
        <header className="flex items-center justify-between mb-6">
          <h1 className="text-3xl font-bold">Ajedrez Academy</h1>
          <nav className="space-x-3">
            <button onClick={() => setTab("home")} className={`px-3 py-1 rounded ${tab==="home"?"bg-indigo-600 text-white":"bg-white border"}`}>Inicio</button>
            <button onClick={() => setTab("lessons")} className={`px-3 py-1 rounded ${tab==="lessons"?"bg-indigo-600 text-white":"bg-white border"}`}>Lecciones</button>
            <button onClick={() => setTab("upload")} className={`px-3 py-1 rounded ${tab==="upload"?"bg-indigo-600 text-white":"bg-white border"}`}>Subir</button>
            <button onClick={() => setTab("training")} className={`px-3 py-1 rounded ${tab==="training"?"bg-indigo-600 text-white":"bg-white border"}`}>Entrenamientos</button>
          </nav>
        </header>

        <main className="grid grid-cols-1 md:grid-cols-3 gap-6">
          <section className="md:col-span-2">
            {tab === "home" && (
              <div>
                <h2 className="text-xl font-semibold mb-3">Bienvenido a Ajedrez Academy</h2>
                <p className="mb-4">Aprendé aperturas, finales y tácticas. Subí tus lecciones y organizá entrenamientos con otros jugadores.</p>
                <div className="flex gap-4 items-start">
                  <div>
                    <h3 className="font-medium">Lecciones destacadas</h3>
                    <div className="mt-3 grid grid-cols-1 gap-3">
                      {lessons.slice(0,3).map(l => (
                        <article key={l.id} className="p-3 border rounded bg-white">
                          <h4 className="font-semibold">{l.title}</h4>
                          <p className="text-sm text-gray-600">{l.description}</p>
                        </article>
                      ))}
                      {lessons.length===0 && <p className="text-sm text-gray-500">No hay lecciones todavía.</p>}
                    </div>
                  </div>

                  <div className="ml-6">
                    <h3 className="font-medium">Tablero interactivo</h3>
                    <div className="mt-2">
                      <Chessboard />
                      <small className="text-xs text-gray-500">Click en una pieza para seleccionarla, luego en la casilla destino para moverla (prototipo).</small>
                    </div>
                  </div>
                </div>
              </div>
            )}

            {tab === "lessons" && (
              <div>
                <div className="flex items-center justify-between mb-4">
                  <h2 className="text-xl font-semibold">Lecciones</h2>
                  <button onClick={() => { setTab('upload'); }} className="px-3 py-1 bg-indigo-600 text-white rounded">Subir nueva</button>
                </div>
                <div className="space-y-3">
                  {lessons.map(lesson => (
                    <div key={lesson.id} className="p-3 bg-white border rounded">
                      <div className="flex justify-between">
                        <div>
                          <h3 className="font-semibold">{lesson.title}</h3>
                          <p className="text-sm text-gray-600">{lesson.description}</p>
                        </div>
                        <div className="text-sm text-gray-500">{new Date(lesson.createdAt||Date.now()).toLocaleString()}</div>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            )}

            {tab === "upload" && (
              <div>
                <h2 className="text-xl font-semibold mb-3">Subir contenido</h2>
                <form onSubmit={handleUpload} className="p-4 bg-white border rounded space-y-3">
                  <div>
                    <label className="block text-sm">Título</label>
                    <input value={uploadState.title} onChange={e => setUploadState(s => ({...s, title: e.target.value}))} className="w-full border rounded px-2 py-1" />
                  </div>
                  <div>
                    <label className="block text-sm">Descripción</label>
                    <textarea value={uploadState.description} onChange={e => setUploadState(s => ({...s, description: e.target.value}))} className="w-full border rounded px-2 py-1" rows={4} />
                  </div>
                  <div>
                    <label className="block text-sm">Archivo (video/pdf) — simulación</label>
                    <input type="file" disabled className="w-full text-sm text-gray-500" />
                    <small className="text-xs text-gray-500">En producción subirías a un backend / storage (S3, Cloud Storage, etc.).</small>
                  </div>
                  <div>
                    <button type="submit" className="px-4 py-2 bg-green-600 text-white rounded">Subir (simulado)</button>
                  </div>
                </form>
              </div>
            )}

            {tab === "training" && (
              <div>
                <h2 className="text-xl font-semibold mb-3">Entrenamientos</h2>
                <CreateSessionForm onCreate={createSession} />
                <div className="mt-4 space-y-3">
                  {sessions.length===0 && <p className="text-sm text-gray-500">No hay sesiones. Creá una para empezar.</p>}
                  {sessions.map(s => (
                    <div key={s.id} className="p-3 bg-white border rounded flex justify-between items-center">
                      <div>
                        <div className="font-semibold">{s.title}</div>
                        <div className="text-sm text-gray-500">{s.datetime || 'Hora no establecida'}</div>
                        <div className="text-xs text-gray-600">Jugadores: {s.players.length}</div>
                      </div>
                      <div className="flex flex-col items-end gap-2">
                        <button onClick={() => joinSession(s.id, 'Invitado')} className="px-3 py-1 bg-indigo-600 text-white rounded">Unirse</button>
                        <small className="text-xs text-gray-500">(Prototipo: la señal real usaría WebSockets)</small>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            )}
          </section>

          <aside className="space-y-4">
            <div className="p-4 bg-white border rounded">
              <h3 className="font-semibold">Perfil</h3>
              <p className="text-sm text-gray-600">Usuario local (demo)</p>
              <div className="mt-2">
                <button className="px-3 py-1 bg-gray-100 rounded">Editar perfil</button>
              </div>
            </div>

            <div className="p-4 bg-white border rounded">
              <h3 className="font-semibold">Buscar lecciones</h3>
              <SearchLessons lessons={lessons} onOpen={l => { alert('Abrir lección: '+l.title) }} />
            </div>

            <div className="p-4 bg-white border rounded">
              <h3 className="font-semibold">Estadísticas (simuladas)</h3>
              <p className="text-sm text-gray-600">Lecciones: {lessons.length}</p>
              <p className="text-sm text-gray-600">Sesiones activas: {sessions.length}</p>
            </div>
          </aside>
        </main>
      </div>
    </div>
  );
}

function sampleLessons() {
  return [
    { id: 1, title: "Aperturas básicas: 1.e4", description: "Ideas principales de 1.e4 y respuestas comunes.", createdAt: new Date().toISOString(), author: "Coach" },
    { id: 2, title: "Táctica: clavadas y tenedor", description: "Ejercicios para detectar tácticas tácticas en posiciones abiertas.", createdAt: new Date().toISOString(), author: "Coach" },
    { id: 3, title: "Finales: Rey y peón vs Rey", description: "Conceptos clásicos para material reducido.", createdAt: new Date().toISOString(), author: "Coach" },
  ];
}

function CreateSessionForm({ onCreate }) {
  const [title, setTitle] = useState("");
  const [datetime, setDatetime] = useState("");
  return (
    <form onSubmit={(e) => { e.preventDefault(); if (!title) return alert('Agregá un título'); onCreate({ title, datetime }); setTitle(''); setDatetime(''); }} className="p-3 bg-white border rounded">
      <div className="mb-2">
        <input placeholder="Título de la sesión" value={title} onChange={e => setTitle(e.target.value)} className="w-full border rounded px-2 py-1" />
      </div>
      <div className="mb-2">
        <input placeholder="Fecha y hora (ej: 2025-11-20 18:00)" value={datetime} onChange={e => setDatetime(e.target.value)} className="w-full border rounded px-2 py-1" />
      </div>
      <div className="flex justify-end">
        <button type="submit" className="px-3 py-1 bg-indigo-600 text-white rounded">Crear sesión</button>
      </div>
    </form>
  );
}

function SearchLessons({ lessons, onOpen }) {
  const [q, setQ] = useState("");
  const filtered = lessons.filter(l => l.title.toLowerCase().includes(q.toLowerCase()) || l.description.toLowerCase().includes(q.toLowerCase()));
  return (
    <div>
      <input placeholder="Buscar..." value={q} onChange={e=>setQ(e.target.value)} className="w-full border rounded px-2 py-1 mb-2" />
      <div className="max-h-40 overflow-auto space-y-2">
        {filtered.map(l => (
          <div key={l.id} className="p-2 flex justify-between items-center border rounded bg-gray-50">
            <div>
              <div className="text-sm font-medium">{l.title}</div>
              <div className="text-xs text-gray-500">{l.author}</div>
            </div>
            <button onClick={()=>onOpen(l)} className="text-sm text-indigo-600">Abrir</button>
          </div>
        ))}
        {filtered.length===0 && <div className="text-xs text-gray-500">No hay resultados</div>}
      </div>
    </div>
  );
}
