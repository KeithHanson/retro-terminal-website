<script>
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
  <link rel="icon" href={favicon} />
  <style>
      * {
          margin: 0;
          padding: 0;
          box-sizing: border-box;
      }

      body {
          background: linear-gradient(135deg, #8b7355 0%, #6b5344 100%);
          display: flex;
          justify-content: center;
          align-items: center;
          min-height: 100vh;
          font-family: 'Courier New', monospace;
          padding: 20px;
      }

      .monitor {
          background: linear-gradient(180deg, #e8dcc8 0%, #d4c4a8 50%, #c4b498 100%);
          border-radius: 3px;
          padding: 50px 40px 80px 40px;
          box-shadow: 
              0 25px 70px rgba(0, 0, 0, 0.6),
              inset 0 2px 0 rgba(255, 255, 255, 0.3),
              inset 0 -3px 8px rgba(0, 0, 0, 0.2),
              inset 3px 0 8px rgba(0, 0, 0, 0.15),
              inset -3px 0 8px rgba(0, 0, 0, 0.15);
          position: relative;
          max-width: 900px;
          width: 100%;
      }

      .monitor::before {
          content: 'VT-220';
          position: absolute;
          bottom: 35px;
          left: 40px;
          font-family: Arial, sans-serif;
          font-size: 11px;
          font-weight: bold;
          color: #6b5344;
          letter-spacing: 2px;
      }

      .monitor::after {
          content: '';
          position: absolute;
          top: 20px;
          right: 40px;
          width: 8px;
          height: 8px;
          background: #ff3300;
          border-radius: 50%;
          box-shadow: 
              0 0 8px #ff3300,
              inset 0 -1px 2px rgba(0, 0, 0, 0.5),
              inset 0 1px 1px rgba(255, 255, 255, 0.3);
          animation: powerLight 2s ease-in-out infinite;
      }

      @keyframes powerLight {
          0%, 100% { opacity: 1; }
          50% { opacity: 0.8; }
      }

      .screen-bezel {
          background: linear-gradient(135deg, #2a2a2a 0%, #1a1a1a 100%);
          border-radius: 2px;
          padding: 25px;
          box-shadow: 
              inset 0 4px 12px rgba(0, 0, 0, 0.9),
              inset 0 0 0 2px #3a3a3a,
              inset 4px 4px 8px rgba(0, 0, 0, 0.8),
              inset -2px -2px 6px rgba(80, 80, 80, 0.3);
          border: 3px solid #4a4a4a;
      }

      .screen {
          background: #000a00;
          border-radius: 0;
          padding: 20px;
          min-height: 400px;
          position: relative;
          overflow: hidden;
          box-shadow: 
              inset 0 0 80px rgba(0, 255, 0, 0.08),
              0 0 30px rgba(0, 255, 0, 0.15);
          border: 1px solid #001a00;
      }

      .screen::before {
          content: '';
          position: absolute;
          top: 0;
          left: 0;
          right: 0;
          bottom: 0;
          background: linear-gradient(
              transparent 50%,
              rgba(0, 0, 0, 0.25) 50%
          );
          background-size: 100% 3px;
          pointer-events: none;
          animation: scanlines 10s linear infinite;
          z-index: 2;
      }

      @keyframes scanlines {
          0% { transform: translateY(0); }
          100% { transform: translateY(3px); }
      }

      .screen::after {
          content: '';
          position: absolute;
          top: 0;
          left: 0;
          right: 0;
          bottom: 0;
          background: radial-gradient(ellipse at center, transparent 0%, rgba(0, 0, 0, 0.4) 100%);
          pointer-events: none;
          z-index: 2;
      }

      #terminal {
          position: relative;
          z-index: 1;
      }

      /* Override xterm.js styles for 1980s look */
      .xterm {
          font-family: 'Courier New', monospace !important;
          font-size: 15px !important;
          line-height: 1.4 !important;
          letter-spacing: 0.5px !important;
      }

      .xterm .xterm-viewport {
          background-color: transparent !important;
          overflow-y: auto !important;
      }

      .xterm .xterm-screen {
          background-color: transparent !important;
      }

      .xterm .xterm-cursor-layer .xterm-cursor {
          background-color: #33ff33 !important;
      }

      .xterm .xterm-rows {
          white-space: pre-wrap !important;
          word-wrap: break-word !important;
      }

      .xterm .xterm-row {
          white-space: pre-wrap !important;
          word-wrap: break-word !important;
      }
  </style>
</svelte:head>
{@render children()}
