# Kkkkjj
Jsjwjwjjs
<!DOCTYPE html>
<html lang="bn">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>৩৬-Key ভার্চুয়াল পিয়ানো</title>

<script src="https://cdn.tailwindcss.com"></script>

<style>
.piano-container { perspective: 1000px; }

.key {
    position: relative;
    cursor: pointer;
    user-select: none;
    display: flex;
    align-items: flex-end;
    justify-content: center;
    padding-bottom: 10px;
    font-weight: bold;
    border-radius: 0 0 6px 6px;
    transition: all .08s ease;
    overflow: hidden;
}

/* Girl image overlay */
.key::before {
    content: "";
    position: absolute;
    inset: 0;
    background-image: url("https://i.imgur.com/8Km9tLL.png");
    background-size: cover;
    background-position: center;
    opacity: 0.18;
    pointer-events: none;
}

.white-key {
    width: 55px;
    height: 210px;
    background: white;
    border: 1px solid #ccc;
    z-index: 1;
}

.white-key.active {
    background: #e5e7eb;
    height: 205px;
}

.black-key {
    width: 36px;
    height: 130px;
    background: #111;
    margin-left: -18px;
    margin-right: -18px;
    z-index: 2;
    color: white;
    font-size: 11px;
}

.black-key.active {
    background: #333;
    height: 125px;
}
</style>
</head>

<body class="bg-gray-900 min-h-screen flex flex-col items-center justify-center p-4">

<h1 class="text-3xl font-bold text-white mb-6">🎹 ৩৬-Key ভার্চুয়াল পিয়ানো</h1>

<div class="piano-container bg-gray-800 p-6 rounded-xl shadow-xl">
    <div id="piano" class="flex"></div>
</div>

<script>
const piano = document.getElementById("piano");
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

const NOTES = [
  { n: "C",  s:false }, { n: "C#", s:true },
  { n: "D",  s:false }, { n: "D#", s:true },
  { n: "E",  s:false },
  { n: "F",  s:false }, { n: "F#", s:true },
  { n: "G",  s:false }, { n: "G#", s:true },
  { n: "A",  s:false }, { n: "A#", s:true },
  { n: "B",  s:false },
];

const active = {};

// Equal temperament formula
function freqFromNote(noteIndex) {
    return 440 * Math.pow(2, (noteIndex - 57) / 12);
}

let noteIndex = 36; // C3

for (let octave = 3; octave <= 5; octave++) {
    NOTES.forEach(note => {
        const freq = freqFromNote(noteIndex);
        const el = document.createElement("div");

        el.className = `key ${note.s ? "black-key" : "white-key"}`;
        el.textContent = note.n + octave;

        el.onmousedown = () => play(freq, el, noteIndex);
        el.onmouseup = () => el.classList.remove("active");
        el.onmouseleave = () => el.classList.remove("active");

        piano.appendChild(el);
        noteIndex++;
    });
}

function play(freq, el, id) {
    if (active[id]) return;

    const osc = audioCtx.createOscillator();
    const gain = audioCtx.createGain();

    osc.type = "triangle";
    osc.frequency.value = freq;

    gain.gain.setValueAtTime(0.4, audioCtx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 1.4);

    osc.connect(gain);
    gain.connect(audioCtx.destination);

    osc.start();
    osc.stop(audioCtx.currentTime + 1.4);

    active[id] = osc;
    el.classList.add("active");

    setTimeout(() => delete active[id], 150);
}

document.body.addEventListener("click", () => {
    if (audioCtx.state === "suspended") audioCtx.resume();
}, { once:true });
</script>

</body>
</html>
