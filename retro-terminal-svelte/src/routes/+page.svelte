<script>
  import * as XTerm from '@xterm/xterm';
  import { browser } from '$app/environment';

  if (browser) {
    const term = new XTerm.Terminal({
        cursorBlink: true,
        cursorStyle: 'block',
        theme: {
            background: 'transparent',
            foreground: '#33ff33',
            cursor: '#33ff33',
            cursorAccent: '#000a00',
            selectionBackground: 'rgba(51, 255, 51, 0.3)'
        },
        fontFamily: '"Courier New", monospace',
        fontSize: 15,
        lineHeight: 1.4,
        letterSpacing: 0.5,
        cols: 80,
        rows: 24,
        convertEol: true,
        scrollback: 1000
    });

    term.open(document.getElementById('terminal'));

    term.writeln('DEC VT-220 Compatible Terminal');
    term.writeln('ROM Version 2.3');
    term.writeln('Copyright (c) Digital Equipment Corp. 1988');
    term.writeln('');
    term.writeln('Performing self-test................ OK');
    term.writeln('Keyboard initialized................ OK');
    term.writeln('');
    term.focus();

    // Command buffer
    let currentLine = '';
    let commandHistory = [];
    let historyIndex = -1;

    // Show prompt
    function prompt() {
        term.write('$ ');
    }

    // Utility function to wrap text to specified width
    function wrapText(text, maxWidth) {
        if (!text) return [''];
        
        const lines = [];
        const paragraphs = text.split('\n');
        
        paragraphs.forEach(paragraph => {
            if (!paragraph.trim()) {
                lines.push('');
                return;
            }
            
            const words = paragraph.split(' ');
            let currentLine = '';

            for (const word of words) {
                if (word.length > maxWidth) {
                    // If a single word is longer than maxWidth, break it
                    if (currentLine.trim()) {
                        lines.push(currentLine.trim());
                        currentLine = '';
                    }
                    for (let i = 0; i < word.length; i += maxWidth) {
                        lines.push(word.slice(i, i + maxWidth));
                    }
                } else if ((currentLine + word).length > maxWidth) {
                    lines.push(currentLine.trim());
                    currentLine = word + ' ';
                } else {
                    currentLine += word + ' ';
                }
            }

            if (currentLine.trim()) {
                lines.push(currentLine.trim());
            }
        });

        return lines.length > 0 ? lines : [''];
    }

    function handleCommand(command) {
        command = command.trim();
        
        if (!command) {
            return '';
        }

        // Add to history
        commandHistory.push(command);
        historyIndex = commandHistory.length;

        // Simple command responses
        const commands = {
            help: 'Available commands: help, clear, date, echo [text], hello, about',
            date: new Date().toString(),
            hello: 'Hello, user! Welcome to the retro terminal.',
            about: 'Skeuomorphic Terminal - A nostalgic throwback to the computing of yesteryear. This terminal emulator recreates the look and feel of 1980s computer terminals with authentic green phosphor display, scanlines, and period-appropriate beige monitor casing.',
        };

        // Handle clear command
        if (command.toLowerCase() === 'clear') {
            term.clear();
            return '';
        }

        // Handle echo command
        if (command.toLowerCase().startsWith('echo ')) {
            return command.substring(5);
        }

        // Check built-in commands
        const cmd = command.toLowerCase();
        if (commands[cmd]) {
            return commands[cmd];
        }

        // Default response for unknown commands
        return `Command not found: ${command}. Type 'help' for available commands.`;
    }

    prompt();


    term.onData(data => {
        const code = data.charCodeAt(0);

        // Handle Enter key
        if (code === 13) {
            term.write('\r\n');
            const output = handleCommand(currentLine);
            if (output) {
                // Wrap long output text
                const lines = wrapText(output, 80);
                lines.forEach(line => term.writeln(line));
            }
            currentLine = '';
            prompt();
        }
        // Handle Backspace
        else if (code === 127) {
            if (currentLine.length > 0) {
                currentLine = currentLine.slice(0, -1);
                term.write('\b \b');
            }
        }
        // Handle Ctrl+C
        else if (code === 3) {
            term.write('^C\r\n');
            currentLine = '';
            prompt();
        }
        // Handle Up Arrow (previous command in history)
        else if (data === '\x1b[A') {
            if (historyIndex > 0) {
                // Clear current line
                term.write('\r\x1b[K');
                prompt();
                
                historyIndex--;
                currentLine = commandHistory[historyIndex];
                term.write(currentLine);
            }
        }
        // Handle Down Arrow (next command in history)
        else if (data === '\x1b[B') {
            // Clear current line
            term.write('\r\x1b[K');
            prompt();
            
            if (historyIndex < commandHistory.length - 1) {
                historyIndex++;
                currentLine = commandHistory[historyIndex];
                term.write(currentLine);
            } else {
                historyIndex = commandHistory.length;
                currentLine = '';
            }
        }
        // Handle regular characters
        else if (code >= 32 && code < 127) {
            currentLine += data;
            term.write(data);
        }
    });

  }
</script>

<div class="monitor">
    <div class="screen-bezel">
        <div class="screen">
            <div id="terminal"></div>
        </div>
    </div>
</div>
