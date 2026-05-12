import { useState, useEffect, useRef, useCallback } from "react";

const TOUR_SCENES = [
  {
    id: "hotel",
    label: "Luxury Hotel Suite",
    industry: "Hotels",
    icon: "🏨",
    color: "#c9a96e",
    image: "https://images.unsplash.com/photo-1631049307264-da0ec9d70304?w=2000&q=90",
    hotspots: [
      { x: 30, y: 45, label: "King Bed", icon: "🛏️" },
      { x: 70, y: 55, label: "City View", icon: "🌆" },
      { x: 55, y: 35, label: "Minibar", icon: "🥂" },
    ],
  },
  {
    id: "cafe",
    label: "Artisan Café",
    industry: "Cafés",
    icon: "☕",
    color: "#a0785a",
    image: "https://images.unsplash.com/photo-1501339847302-ac426a4a7cbb?w=2000&q=90",
    hotspots: [
      { x: 25, y: 60, label: "Espresso Bar", icon: "☕" },
      { x: 65, y: 40, label: "Cozy Seats", icon: "🪑" },
      { x: 50, y: 70, label: "Pastry Display", icon: "🥐" },
    ],
  },
  {
    id: "gym",
    label: "Premium Gym",
    industry: "Gyms",
    icon: "💪",
    color: "#5e8fa8",
    image: "https://images.unsplash.com/photo-1534438327276-14e5300c3a48?w=2000&q=90",
    hotspots: [
      { x: 20, y: 50, label: "Free Weights", icon: "🏋️" },
      { x: 60, y: 45, label: "Cardio Zone", icon: "🏃" },
      { x: 42, y: 65, label: "Recovery Area", icon: "🧘" },
    ],
  },
  {
    id: "realestate",
    label: "Modern Penthouse",
    industry: "Real Estate",
    icon: "🏙️",
    color: "#7e9b7e",
    image: "https://images.unsplash.com/photo-1600596542815-ffad4c1539a9?w=2000&q=90",
    hotspots: [
      { x: 35, y: 55, label: "Living Area", icon: "🛋️" },
      { x: 68, y: 42, label: "Panorama View", icon: "🌇" },
      { x: 50, y: 30, label: "Kitchen", icon: "🍳" },
    ],
  },
  {
    id: "resort",
    label: "Tropical Resort",
    industry: "Resorts",
    icon: "🌴",
    color: "#4a9e8a",
    image: "https://images.unsplash.com/photo-1520250497591-112f2f40a3f4?w=2000&q=90",
    hotspots: [
      { x: 30, y: 60, label: "Infinity Pool", icon: "🏊" },
      { x: 65, y: 50, label: "Beach Access", icon: "🏖️" },
      { x: 48, y: 35, label: "Spa Lounge", icon: "💆" },
    ],
  },
  {
    id: "school",
    label: "Modern Campus",
    industry: "Schools",
    icon: "🎓",
    color: "#8a7ab5",
    image: "https://images.unsplash.com/photo-1562774053-701939374585?w=2000&q=90",
    hotspots: [
      { x: 28, y: 48, label: "Lecture Hall", icon: "🎓" },
      { x: 62, y: 55, label: "Innovation Lab", icon: "🔬" },
      { x: 48, y: 38, label: "Library", icon: "📚" },
    ],
  },
];

const INDUSTRIES = [
  { name: "Hotels", icon: "🏨", desc: "Showcase every suite & amenity" },
  { name: "Cafés", icon: "☕", desc: "Invite guests before they arrive" },
  { name: "Gyms", icon: "💪", desc: "Tour equipment & studio spaces" },
  { name: "Schools", icon: "🎓", desc: "Virtual open days & campus tours" },
  { name: "Resorts", icon: "🌴", desc: "Immersive destination previews" },
  { name: "Real Estate", icon: "🏙️", desc: "Walk-through from anywhere" },
];

function useParallax() {
  const [offset, setOffset] = useState({ x: 0, y: 0 });
  useEffect(() => {
    const handle = (e) => {
      setOffset({
        x: (e.clientX / window.innerWidth - 0.5) * 20,
        y: (e.clientY / window.innerHeight - 0.5) * 10,
      });
    };
    window.addEventListener("mousemove", handle);
    return () => window.removeEventListener("mousemove", handle);
  }, []);
  return offset;
}

function LoadingSkeleton() {
  return (
    <div style={{
      position: "absolute", inset: 0,
      background: "linear-gradient(135deg, #0d0d0d 0%, #1a1a1a 100%)",
      display: "flex", flexDirection: "column",
      alignItems: "center", justifyContent: "center", gap: 24, zIndex: 10,
    }}>
      <div style={{ position: "relative", width: 64, height: 64 }}>
        {[0, 1, 2].map(i => (
          <div key={i} style={{
            position: "absolute", inset: 0,
            border: `2px solid rgba(255,255,255,${0.15 + i * 0.1})`,
            borderRadius: "50%",
            animation: `pulse-ring 1.6s ease-out ${i * 0.3}s infinite`,
          }} />
        ))}
        <div style={{
          position: "absolute", inset: "24%",
          background: "rgba(255,255,255,0.9)", borderRadius: "50%",
        }} />
      </div>
      <div style={{ textAlign: "center" }}>
        <p style={{
          fontFamily: "'Cormorant Garamond', serif", fontSize: 13,
          letterSpacing: "0.25em", textTransform: "uppercase",
          color: "rgba(255,255,255,0.5)", margin: 0,
          animation: "fade-pulse 1.4s ease-in-out infinite",
        }}>Preparing Immersive Tour</p>
      </div>
    </div>
  );
}

function Hotspot({ x, y, label, icon, visible }) {
  const [hovered, setHovered] = useState(false);
  return (
    <div
      style={{
        position: "absolute", left: `${x}%`, top: `${y}%`,
        transform: "translate(-50%,-50%)",
        transition: "opacity 0.5s ease",
        opacity: visible ? 1 : 0,
        zIndex: 5,
        pointerEvents: visible ? "auto" : "none",
      }}
      onMouseEnter={() => setHovered(true)}
      onMouseLeave={() => setHovered(false)}
    >
      <div style={{
        width: 40, height: 40,
        background: hovered ? "rgba(255,255,255,0.25)" : "rgba(255,255,255,0.12)",
        border: "1.5px solid rgba(255,255,255,0.5)",
        borderRadius: "50%",
        backdropFilter: "blur(12px)",
        display: "flex", alignItems: "center", justifyContent: "center",
        cursor: "pointer",
        transition: "all 0.3s ease",
        transform: hovered ? "scale(1.2)" : "scale(1)",
        boxShadow: hovered ? "0 0 20px rgba(255,255,255,0.3)" : "0 0 10px rgba(0,0,0,0.3)",
        animation: "hotspot-pulse 2.5s ease-in-out infinite",
      }}>
        <span style={{ fontSize: 16 }}>{icon}</span>
      </div>
      {hovered && (
        <div style={{
          position: "absolute", left: "50%", bottom: "calc(100% + 8px)",
          transform: "translateX(-50%)",
          background: "rgba(10,10,10,0.9)",
          border: "1px solid rgba(255,255,255,0.15)",
          borderRadius: 8, padding: "6px 14px",
          whiteSpace: "nowrap",
          backdropFilter: "blur(20px)",
        }}>
          <p style={{
            fontFamily: "'DM Sans', sans-serif", fontSize: 12,
            color: "#fff", margin: 0, letterSpacing: "0.05em",
          }}>{label}</p>
        </div>
      )}
    </div>
  );
}

function TourViewer({ scene, isLoading, isDragging, dragOffset, onMouseDown }) {
  const [imgLoaded, setImgLoaded] = useState(false);
  const [currentSrc, setCurrentSrc] = useState(scene.image);

  useEffect(() => {
    setImgLoaded(false);
    setCurrentSrc(scene.image);
  }, [scene.image]);

  const fullyLoaded = !isLoading && imgLoaded;

  return (
    <div style={{
      position: "relative", width: "100%", height: "100%",
      overflow: "hidden", borderRadius: "inherit",
    }}>
      {(!fullyLoaded) && <LoadingSkeleton />}

      <div
        onMouseDown={onMouseDown}
        style={{
          position: "absolute", inset: 0,
          cursor: isDragging ? "grabbing" : "grab",
          userSelect: "none",
        }}
      >
        <img
          src={currentSrc}
          alt={scene.label}
          draggable={false}
          onLoad={() => setImgLoaded(true)}
          style={{
            position: "absolute",
            width: "140%", height: "120%",
            top: "-10%", left: `${-20 + dragOffset.x * 0.05}%`,
            objectFit: "cover",
            transition: isDragging ? "none" : "left 0.1s ease, transform 0.6s ease",
            transform: `translateY(${dragOffset.y * 0.03}%) scale(1.02)`,
            filter: !fullyLoaded ? "blur(8px) brightness(0.6)" : "brightness(0.75)",
            opacity: fullyLoaded ? 1 : 0,
            willChange: "left, transform",
          }}
        />
      </div>

      <div style={{
        position: "absolute", inset: 0, pointerEvents: "none",
        background: "radial-gradient(ellipse at center, transparent 30%, rgba(0,0,0,0.7) 100%)",
      }} />

      <div style={{
        position: "absolute", bottom: 0, left: 0, right: 0, height: "45%",
        background: "linear-gradient(to top, rgba(0,0,0,0.9) 0%, transparent 100%)",
        pointerEvents: "none",
      }} />

      <div style={{
        position: "absolute", top: 0, left: 0, right: 0, height: "25%",
        background: "linear-gradient(to bottom, rgba(0,0,0,0.6) 0%, transparent 100%)",
        pointerEvents: "none",
      }} />

      {scene.hotspots.map((h, i) => (
        <Hotspot key={i} {...h} visible={fullyLoaded} />
      ))}

      {fullyLoaded && (
        <div style={{
          position: "absolute", top: "50%", left: "50%",
          transform: "translate(-50%, -50%)",
          pointerEvents: "none",
          animation: "fade-in-out 3s ease forwards",
          textAlign: "center",
        }}>
          <div style={{
            background: "rgba(0,0,0,0.5)",
            border: "1px solid rgba(255,255,255,0.2)",
            borderRadius: 50, padding: "10px 24px",
            backdropFilter: "blur(20px)",
            display: "flex", alignItems: "center", gap: 10,
          }}>
            <span style={{ fontSize: 20 }}>⟺</span>
            <span style={{
              fontFamily: "'DM Sans', sans-serif",
              fontSize: 13, letterSpacing: "0.15em",
              textTransform: "uppercase", color: "rgba(255,255,255,0.9)",
            }}>Drag to Explore</span>
          </div>
        </div>
      )}

      <div style={{
        position: "absolute", bottom: 28, left: 28,
        opacity: fullyLoaded ? 1 : 0,
        transition: "opacity 0.8s ease 0.3s",
      }}>
        <p style={{
          fontFamily: "'DM Sans', sans-serif", fontSize: 11,
          letterSpacing: "0.25em", textTransform: "uppercase",
          color: scene.color, margin: "0 0 6px",
        }}>{scene.industry}</p>
        <h3 style={{
          fontFamily: "'Cormorant Garamond', serif",
          fontSize: 28, fontWeight: 500,
          color: "#fff", margin: 0, lineHeight: 1.1,
        }}>{scene.label}</h3>
      </div>

      <div style={{
        position: "absolute", top: 20, right: 20,
        background: "rgba(0,0,0,0.5)",
        border: "1px solid rgba(255,255,255,0.2)",
        borderRadius: 20, padding: "6px 14px",
        backdropFilter: "blur(20px)",
      }}>
        <span style={{
          fontFamily: "'DM Sans', sans-serif", fontSize: 12,
          letterSpacing: "0.12em", color: "rgba(255,255,255,0.9)",
        }}>◉ 360°</span>
      </div>
    </div>
  );
}

export default function VirtualTourWebsite() {
  const [activeScene, setActiveScene] = useState(0);
  const [isLoading, setIsLoading] = useState(true);
  const [isDragging, setIsDragging] = useState(false);
  const [dragOffset, setDragOffset] = useState({ x: 0, y: 0 });
  const [scrollY, setScrollY] = useState(0);
  const dragOffsetRef = useRef({ x: 0, y: 0 });
  const isDraggingRef = useRef(false);
  const dragStartRef = useRef({ x: 0, y: 0 });
  const parallax = useParallax();
  const containerRef = useRef(null);

  useEffect(() => {
    setIsLoading(true);
    const t = setTimeout(() => setIsLoading(false), 1200);
    return () => clearTimeout(t);
  }, [activeScene]);

  useEffect(() => {
    const handleScroll = () => setScrollY(window.scrollY);
    window.addEventListener("scroll", handleScroll);
    return () => window.removeEventListener("scroll", handleScroll);
  }, []);

  const handleMouseDown = useCallback((e) => {
    isDraggingRef.current = true;
    setIsDragging(true);
    dragStartRef.current = {
      x: e.clientX - dragOffsetRef.current.x,
      y: e.clientY - dragOffsetRef.current.y,
    };
  }, []);

  useEffect(() => {
    const handleMouseMove = (e) => {
      if (!isDraggingRef.current) return;
      const newOffset = {
        x: Math.max(-120, Math.min(120, e.clientX - dragStartRef.current.x)),
        y: Math.max(-40, Math.min(40, e.clientY - dragStartRef.current.y)),
      };
      dragOffsetRef.current = newOffset;
      setDragOffset(newOffset);
    };
    const handleMouseUp = () => {
      isDraggingRef.current = false;
      setIsDragging(false);
    };
    window.addEventListener("mousemove", handleMouseMove);
    window.addEventListener("mouseup", handleMouseUp);
    return () => {
      window.removeEventListener("mousemove", handleMouseMove);
      window.removeEventListener("mouseup", handleMouseUp);
    };
  }, []);

  const scene = TOUR_SCENES[activeScene];

  return (
    <>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@300;400;500;600&family=DM+Sans:wght@300;400;500&display=swap');

        *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

        :root {
          --gold: #c9a96e;
          --gold-light: #e8d4a8;
          --dark: #080808;
          --dark-2: #111111;
          --dark-3: #1a1a1a;
          --dark-4: #242424;
          --text: rgba(255,255,255,0.92);
          --text-muted: rgba(255,255,255,0.45);
          --border: rgba(255,255,255,0.08);
        }

        body { background: var(--dark); color: var(--text); scroll-behavior: smooth; }

        @keyframes pulse-ring {
          0% { transform: scale(0.85); opacity: 1; }
          100% { transform: scale(1.6); opacity: 0; }
        }
        @keyframes fade-pulse {
          0%,100% { opacity: 0.4; }
          50% { opacity: 0.8; }
        }
        @keyframes hotspot-pulse {
          0%,100% { box-shadow: 0 0 0 0 rgba(255,255,255,0.2); }
          50% { box-shadow: 0 0 0 8px rgba(255,255,255,0); }
        }
        @keyframes fade-in-out {
          0% { opacity: 0; }
          20% { opacity: 1; }
          70% { opacity: 1; }
          100% { opacity: 0; }
        }
        @keyframes float-up {
          from { opacity: 0; transform: translateY(30px); }
          to { opacity: 1; transform: translateY(0); }
        }
        @keyframes shimmer {
          0% { transform: translateX(-100%); }
          100% { transform: translateX(200%); }
        }
        @keyframes rotate-slow {
          from { transform: rotate(0deg); }
          to { transform: rotate(360deg); }
        }
        @keyframes particle-float {
          0%,100% { transform: translateY(0) translateX(0); opacity: 0; }
          10% { opacity: 1; }
          90% { opacity: 0.3; }
          100% { transform: translateY(-120px) translateX(30px); }
        }
        @keyframes gradient-shift {
          0%,100% { background-position: 0% 50%; }
          50% { background-position: 100% 50%; }
        }
        @keyframes slide-in-left {
          from { opacity: 0; transform: translateX(-20px); }
          to { opacity: 1; transform: translateX(0); }
        }

        .page { 
          min-height: 100vh;
          font-family: 'DM Sans', sans-serif;
          overflow-x: hidden;
        }

        .nav {
          position: fixed; top: 0; left: 0; right: 0;
          z-index: 100;
          padding: 20px 40px;
          display: flex; align-items: center; justify-content: space-between;
          background: linear-gradient(to bottom, rgba(0,0,0,0.8) 0%, transparent 100%);
          backdrop-filter: blur(0px);
          transition: all 0.4s ease;
        }
        .nav.scrolled {
          background: rgba(8,8,8,0.9);
          backdrop-filter: blur(30px);
          border-bottom: 1px solid var(--border);
          padding: 14px 40px;
        }
        .nav-logo {
          font-family: 'Cormorant Garamond', serif;
          font-size: 22px; font-weight: 500;
          letter-spacing: 0.05em; color: #fff;
          display: flex; align-items: center; gap: 10px;
        }
        .logo-dot {
          width: 8px; height: 8px; border-radius: 50%;
          background: var(--gold);
          animation: pulse-ring 2s ease infinite;
        }
        .nav-links {
          display: flex; align-items: center; gap: 32px; list-style: none;
        }
        .nav-links a {
          font-size: 13px; letter-spacing: 0.1em; text-transform: uppercase;
          color: var(--text-muted); text-decoration: none;
          transition: color 0.3s; cursor: pointer;
        }
        .nav-links a:hover { color: #fff; }
        .nav-cta {
          background: transparent;
          border: 1px solid rgba(255,255,255,0.3);
          color: #fff; border-radius: 50px;
          padding: 10px 24px; font-size: 13px;
          letter-spacing: 0.08em; cursor: pointer;
          transition: all 0.3s ease; font-family: 'DM Sans', sans-serif;
          backdrop-filter: blur(10px);
        }
        .nav-cta:hover {
          background: rgba(255,255,255,0.1);
          border-color: rgba(255,255,255,0.6);
        }

        .hero {
          position: relative; min-height: 100vh;
          display: flex; align-items: center; justify-content: center;
          overflow: hidden;
        }
        .hero-bg {
          position: absolute; inset: 0;
          background: radial-gradient(ellipse at 30% 50%, rgba(201,169,110,0.08) 0%, transparent 60%),
                      radial-gradient(ellipse at 70% 30%, rgba(100,140,200,0.06) 0%, transparent 50%);
        }
        .hero-grid {
          position: absolute; inset: 0; opacity: 0.04;
          background-image: linear-gradient(rgba(255,255,255,0.5) 1px, transparent 1px),
                            linear-gradient(90deg, rgba(255,255,255,0.5) 1px, transparent 1px);
          background-size: 60px 60px;
        }
        .hero-content {
          position: relative; z-index: 2;
          text-align: center; max-width: 800px;
          padding: 120px 24px 60px;
          animation: float-up 1s ease 0.2s both;
        }
        .hero-badge {
          display: inline-flex; align-items: center; gap: 8px;
          background: rgba(201,169,110,0.1);
          border: 1px solid rgba(201,169,110,0.3);
          border-radius: 50px; padding: 8px 20px;
          margin-bottom: 32px;
        }
        .hero-badge span {
          font-size: 11px; letter-spacing: 0.2em; text-transform: uppercase;
          color: var(--gold);
        }
        .hero-title {
          font-family: 'Cormorant Garamond', serif;
          font-size: clamp(52px, 8vw, 96px);
          font-weight: 400; line-height: 0.95;
          color: #fff; margin-bottom: 28px;
        }
        .hero-title em {
          font-style: italic; color: var(--gold);
        }
        .hero-subtitle {
          font-size: 17px; color: var(--text-muted);
          line-height: 1.7; max-width: 520px; margin: 0 auto 48px;
          font-weight: 300;
        }
        .hero-buttons {
          display: flex; gap: 16px; justify-content: center; flex-wrap: wrap;
        }
        .btn-primary {
          background: linear-gradient(135deg, var(--gold) 0%, #e8c87a 50%, var(--gold) 100%);
          background-size: 200%;
          color: #000; border: none; border-radius: 50px;
          padding: 16px 36px; font-size: 14px; font-weight: 500;
          letter-spacing: 0.08em; cursor: pointer;
          transition: all 0.4s ease; font-family: 'DM Sans', sans-serif;
          animation: gradient-shift 3s ease infinite;
        }
        .btn-primary:hover {
          transform: translateY(-2px);
          box-shadow: 0 20px 40px rgba(201,169,110,0.3);
        }
        .btn-secondary {
          background: rgba(255,255,255,0.05);
          color: #fff; border: 1px solid rgba(255,255,255,0.15);
          border-radius: 50px; padding: 16px 36px;
          font-size: 14px; letter-spacing: 0.08em;
          cursor: pointer; transition: all 0.4s ease;
          font-family: 'DM Sans', sans-serif; backdrop-filter: blur(10px);
        }
        .btn-secondary:hover {
          background: rgba(255,255,255,0.1);
          border-color: rgba(255,255,255,0.4);
          transform: translateY(-2px);
        }
        .hero-scroll {
          position: absolute; bottom: 32px; left: 50%;
          transform: translateX(-50%);
          display: flex; flex-direction: column; align-items: center; gap: 8px;
          animation: float-up 1s ease 1s both;
        }
        .scroll-line {
          width: 1px; height: 50px;
          background: linear-gradient(to bottom, transparent, rgba(255,255,255,0.4));
          animation: shimmer 2s ease infinite;
        }

        .tour-section {
          padding: 80px 24px;
          background: var(--dark-2);
        }
        .section-header {
          text-align: center; margin-bottom: 60px;
          animation: float-up 0.8s ease both;
        }
        .section-tag {
          font-size: 11px; letter-spacing: 0.25em; text-transform: uppercase;
          color: var(--gold); margin-bottom: 16px; display: block;
        }
        .section-title {
          font-family: 'Cormorant Garamond', serif;
          font-size: clamp(36px, 5vw, 60px); font-weight: 400;
          color: #fff; line-height: 1.1; margin-bottom: 16px;
        }
        .section-desc {
          font-size: 16px; color: var(--text-muted);
          max-width: 480px; margin: 0 auto; line-height: 1.7;
        }

        .tour-wrapper {
          max-width: 1200px; margin: 0 auto;
        }

        .scene-tabs {
          display: flex; gap: 0; overflow-x: auto;
          border-radius: 16px 16px 0 0; overflow: hidden;
          border: 1px solid var(--border); border-bottom: none;
          scrollbar-width: none;
        }
        .scene-tabs::-webkit-scrollbar { display: none; }
        .scene-tab {
          flex: 1; min-width: 120px; padding: 14px 20px;
          background: var(--dark-3); border: none;
          color: var(--text-muted); cursor: pointer;
          font-family: 'DM Sans', sans-serif; font-size: 13px;
          letter-spacing: 0.05em; transition: all 0.3s ease;
          border-right: 1px solid var(--border);
          display: flex; align-items: center; justify-content: center; gap: 8px;
        }
        .scene-tab:last-child { border-right: none; }
        .scene-tab:hover { background: var(--dark-4); color: #fff; }
        .scene-tab.active {
          background: var(--dark-4); color: #fff;
          border-bottom: 2px solid var(--gold);
        }

        .viewer-container {
          position: relative; height: 520px;
          border-radius: 0 0 20px 20px;
          border: 1px solid var(--border); border-top: none;
          overflow: hidden;
        }

        .scene-controls {
          position: absolute; right: 20px; top: 50%;
          transform: translateY(-50%);
          display: flex; flex-direction: column; gap: 10px; z-index: 5;
        }
        .ctrl-btn {
          width: 40px; height: 40px;
          background: rgba(0,0,0,0.5);
          border: 1px solid rgba(255,255,255,0.15);
          border-radius: 50%; color: rgba(255,255,255,0.8);
          display: flex; align-items: center; justify-content: center;
          cursor: pointer; font-size: 18px; line-height: 1;
          transition: all 0.3s ease; backdrop-filter: blur(20px);
        }
        .ctrl-btn:hover {
          background: rgba(255,255,255,0.15);
          border-color: rgba(255,255,255,0.4);
        }

        .industries-section {
          padding: 100px 24px;
          background: var(--dark);
          position: relative; overflow: hidden;
        }
        .industries-bg {
          position: absolute; inset: 0;
          background: radial-gradient(ellipse at 80% 50%, rgba(201,169,110,0.05) 0%, transparent 60%);
        }
        .industries-grid {
          max-width: 1100px; margin: 0 auto;
          display: grid;
          grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
          gap: 20px;
        }
        .industry-card {
          position: relative; padding: 32px;
          background: rgba(255,255,255,0.03);
          border: 1px solid var(--border);
          border-radius: 20px; cursor: pointer;
          transition: all 0.4s ease;
          overflow: hidden;
        }
        .industry-card::before {
          content: ''; position: absolute; inset: 0;
          background: linear-gradient(135deg, rgba(201,169,110,0.06) 0%, transparent 60%);
          opacity: 0; transition: opacity 0.4s ease;
          border-radius: inherit;
        }
        .industry-card:hover { 
          transform: translateY(-4px);
          border-color: rgba(201,169,110,0.3);
          box-shadow: 0 30px 60px rgba(0,0,0,0.4);
        }
        .industry-card:hover::before { opacity: 1; }
        .industry-icon {
          font-size: 36px; margin-bottom: 20px; display: block;
        }
        .industry-name {
          font-family: 'Cormorant Garamond', serif;
          font-size: 24px; color: #fff; margin-bottom: 10px;
        }
        .industry-desc {
          font-size: 14px; color: var(--text-muted); line-height: 1.6;
        }
        .industry-arrow {
          position: absolute; right: 24px; top: 50%;
          transform: translateY(-50%);
          width: 32px; height: 32px;
          border: 1px solid rgba(255,255,255,0.1);
          border-radius: 50%;
          display: flex; align-items: center; justify-content: center;
          color: var(--text-muted); font-size: 14px;
          transition: all 0.3s ease;
        }
        .industry-card:hover .industry-arrow {
          background: var(--gold); border-color: var(--gold); color: #000;
          transform: translateY(-50%) rotate(45deg);
        }

        .stats-section {
          padding: 80px 24px;
          background: var(--dark-2);
          border-top: 1px solid var(--border);
          border-bottom: 1px solid var(--border);
        }
        .stats-grid {
          max-width: 900px; margin: 0 auto;
          display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
          gap: 40px; text-align: center;
        }
        .stat-num {
          font-family: 'Cormorant Garamond', serif;
          font-size: 56px; font-weight: 300;
          color: var(--gold); line-height: 1;
          margin-bottom: 8px;
        }
        .stat-label {
          font-size: 13px; letter-spacing: 0.12em;
          text-transform: uppercase; color: var(--text-muted);
        }

        .cta-section {
          padding: 120px 24px;
          text-align: center; position: relative; overflow: hidden;
        }
        .cta-glow {
          position: absolute; top: 50%; left: 50%;
          width: 600px; height: 600px;
          transform: translate(-50%, -50%);
          background: radial-gradient(circle, rgba(201,169,110,0.12) 0%, transparent 70%);
          pointer-events: none;
        }
        .cta-title {
          font-family: 'Cormorant Garamond', serif;
          font-size: clamp(40px, 6vw, 72px); font-weight: 400;
          color: #fff; line-height: 1.05; margin-bottom: 24px;
          position: relative; z-index: 1;
        }
        .cta-desc {
          font-size: 17px; color: var(--text-muted);
          margin-bottom: 48px; position: relative; z-index: 1;
          max-width: 460px; margin-left: auto; margin-right: auto;
          line-height: 1.7;
        }
        .cta-buttons {
          display: flex; gap: 16px; justify-content: center;
          flex-wrap: wrap; position: relative; z-index: 1;
        }

        .footer {
          padding: 40px; display: flex;
          align-items: center; justify-content: space-between;
          border-top: 1px solid var(--border);
          flex-wrap: wrap; gap: 20px;
        }
        .footer-copy {
          font-size: 13px; color: var(--text-muted);
        }
        .footer-links {
          display: flex; gap: 24px; list-style: none;
        }
        .footer-links a {
          font-size: 13px; color: var(--text-muted);
          text-decoration: none; cursor: pointer;
          transition: color 0.3s;
        }
        .footer-links a:hover { color: #fff; }

        .glass {
          background: rgba(255,255,255,0.04);
          backdrop-filter: blur(20px);
          border: 1px solid rgba(255,255,255,0.08);
        }

        @media (max-width: 768px) {
          .nav { padding: 16px 20px; }
          .nav-links { display: none; }
          .hero-content { padding: 100px 20px 60px; }
          .scene-tab .tab-text { display: none; }
          .scene-tab { min-width: 50px; padding: 14px 10px; }
          .viewer-container { height: 340px; }
          .industries-grid { grid-template-columns: 1fr; }
          .footer { flex-direction: column; text-align: center; padding: 30px 20px; }
        }
      `}</style>

      <div className="page" ref={containerRef}>
        <nav className={`nav ${scrollY > 60 ? "scrolled" : ""}`}>
          <div className="nav-logo">
            <div className="logo-dot" />
            VistaSphere
          </div>
          <ul className="nav-links">
            {["Tours", "Industries", "Pricing", "About"].map(l => (
              <li key={l}><a href={`#${l.toLowerCase()}`} onClick={(e) => e.preventDefault()}>{l}</a></li>
            ))}
          </ul>
          <button className="nav-cta">Book a Demo</button>
        </nav>

        <section className="hero">
          <div className="hero-bg" style={{
            transform: `translate(${parallax.x * 0.5}px, ${parallax.y * 0.5}px)`,
          }} />
          <div className="hero-grid" />

          {[...Array(12)].map((_, i) => (
            <div key={i} style={{
              position: "absolute",
              left: `${10 + i * 8}%`,
              bottom: `${20 + (i % 4) * 15}%`,
              width: 3, height: 3,
              borderRadius: "50%",
              background: i % 3 === 0 ? "var(--gold)" : "rgba(255,255,255,0.3)",
              animation: `particle-float ${4 + i * 0.7}s ease-in-out ${i * 0.5}s infinite`,
              pointerEvents: "none",
            }} />
          ))}

          <div className="hero-content">
            <div className="hero-badge">
              <span>✦</span>
              <span>The Future of Immersive Experiences</span>
            </div>
            <h1 className="hero-title">
              Step Inside<br />
              <em>Every Space</em><br />
              Before You Visit
            </h1>
            <p className="hero-subtitle">
              Cinematic 360° virtual tours that transform how clients explore hotels, properties, gyms, campuses, and more.
            </p>
            <div className="hero-buttons">
              <button className="btn-primary">View Demo Tour</button>
              <button className="btn-secondary">Book a Free Demo →</button>
            </div>
          </div>

          <div className="hero-scroll">
            <span style={{ fontSize: 11, letterSpacing: "0.2em", color: "var(--text-muted)", textTransform: "uppercase" }}>Explore</span>
            <div className="scroll-line" />
          </div>
        </section>

        <section className="tour-section" id="tours">
          <div className="section-header">
            <span className="section-tag">◈ Live Preview</span>
            <h2 className="section-title">Immersive Tour Experience</h2>
            <p className="section-desc">Drag to navigate any scene. Click hotspots to discover details.</p>
          </div>

          <div className="tour-wrapper">
            <div className="scene-tabs">
              {TOUR_SCENES.map((s, i) => (
                <button
                  key={s.id}
                  className={`scene-tab ${i === activeScene ? "active" : ""}`}
                  onClick={() => { setActiveScene(i); setDragOffset({ x: 0, y: 0 }); dragOffsetRef.current = { x: 0, y: 0 }; }}
                  aria-label={`View ${s.industry} scene`}
                >
                  <span>{s.icon}</span>
                  <span className="tab-text">{s.industry}</span>
                </button>
              ))}
            </div>

            <div className="viewer-container">
              <TourViewer
                scene={scene}
                isLoading={isLoading}
                isDragging={isDragging}
                dragOffset={dragOffset}
                onMouseDown={handleMouseDown}
              />

              <div className="scene-controls">
                <button
                  className="ctrl-btn"
                  onClick={() => setActiveScene(p => (p - 1 + TOUR_SCENES.length) % TOUR_SCENES.length)}
                  aria-label="Previous scene"
                >‹</button>
                <button
                  className="ctrl-btn"
                  onClick={() => setActiveScene(p => (p + 1) % TOUR_SCENES.length)}
                  aria-label="Next scene"
                >›</button>
              </div>

              <div style={{
                position: "absolute", bottom: 24, right: 24,
                display: "flex", gap: 6, zIndex: 5,
              }}>
                {TOUR_SCENES.map((_, i) => (
                  <button
                    key={i}
                    onClick={() => setActiveScene(i)}
                    aria-label={`Go to scene ${i + 1}`}
                    style={{
                      width: i === activeScene ? 20 : 6,
                      height: 6, borderRadius: 3,
                      background: i === activeScene ? "var(--gold)" : "rgba(255,255,255,0.25)",
                      border: "none", cursor: "pointer",
                      transition: "all 0.3s ease", padding: 0,
                    }}
                  />
                ))}
              </div>
            </div>
          </div>
        </section>

        <section className="stats-section">
          <div className="stats-grid">
            {[
              { num: "2.4×", label: "More Bookings" },
              { num: "98%", label: "Client Satisfaction" },
              { num: "500+", label: "Tours Created" },
              { num: "40+", label: "Industries Served" },
            ].map(s => (
              <div key={s.label}>
                <div className="stat-num">{s.num}</div>
                <div className="stat-label">{s.label}</div>
              </div>
            ))}
          </div>
        </section>

        <section className="industries-section" id="industries">
          <div className="industries-bg" />
          <div className="section-header">
            <span className="section-tag">◈ Industries</span>
            <h2 className="section-title">Built for Every Venue</h2>
            <p className="section-desc">Premium 360° experiences crafted for the world's most beautiful spaces.</p>
          </div>

          <div className="industries-grid">
            {INDUSTRIES.map((ind) => (
              <div key={ind.name} className="industry-card">
                <span className="industry-icon">{ind.icon}</span>
                <h3 className="industry-name">{ind.name}</h3>
                <p className="industry-desc">{ind.desc}</p>
                <div className="industry-arrow">→</div>
              </div>
            ))}
          </div>
        </section>

        <section className="cta-section">
          <div className="cta-glow" />
          <h2 className="cta-title">
            Ready to Transform<br />
            <em style={{ fontStyle: "italic", color: "var(--gold)" }}>Your Space?</em>
          </h2>
          <p className="cta-desc" style={{ marginBottom: 48 }}>
            Join 500+ businesses using VistaSphere to convert more clients with immersive 360° experiences.
          </p>
          <div className="cta-buttons">
            <button className="btn-primary" style={{ padding: "18px 48px", fontSize: 15 }}>
              Book a Free Demo
            </button>
            <button className="btn-secondary" style={{ padding: "18px 40px", fontSize: 15 }}>
              View Demo Tour
            </button>
          </div>

          <div style={{
            marginTop: 60, display: "flex", justifyContent: "center",
            gap: 40, flexWrap: "wrap",
          }}>
            {["Trusted by 500+ businesses", "48h delivery", "Unlimited hotspots"].map(t => (
              <div key={t} style={{
                display: "flex", alignItems: "center", gap: 8,
                fontSize: 13, color: "var(--text-muted)",
              }}>
                <span style={{ color: "var(--gold)" }}>✓</span>
                {t}
              </div>
            ))}
          </div>
        </section>

        <footer className="footer">
          <div className="nav-logo" style={{ fontSize: 18 }}>
            <div className="logo-dot" />
            VistaSphere
          </div>
          <p className="footer-copy">© 2026 VistaSphere. Crafted with precision.</p>
          <ul className="footer-links">
            {["Privacy", "Terms", "Contact"].map(l => (
              <li key={l}><a href="#" onClick={(e) => e.preventDefault()}>{l}</a></li>
            ))}
          </ul>
        </footer>
      </div>
    </>
  );
}
