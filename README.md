<html class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Word Chain Game</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://cdn.jsdelivr.net/npm/lucide@0.263.0/dist/umd/lucide.min.js"></script>
  <script src="/_sdk/element_sdk.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;500;600;700&amp;display=swap" rel="stylesheet">
  <style>
    * { font-family: 'Fredoka', sans-serif; }
    @keyframes bounce-in { 0% { transform: scale(0); } 50% { transform: scale(1.15); } 100% { transform: scale(1); } }
    @keyframes shake { 0%,100% { transform: translateX(0); } 25% { transform: translateX(-8px); } 75% { transform: translateX(8px); } }
    .bounce-in { animation: bounce-in 0.4s ease-out; }
    .shake { animation: shake 0.4s ease-out; }
    .word-bubble { background: #fff; border: 3px solid #fbbf24; border-radius: 20px; padding: 8px 16px; display: inline-block; margin: 4px; }
    .chain-connector { color: #f472b6; font-weight: 700; font-size: 1.2rem; }
  </style>
  <style>body { box-sizing: border-box; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
 <script src="/_sdk/telemetry_sdk.js"></script></head>
 <body class="h-full">
  <div id="app" class="h-full w-full overflow-auto" style="background: linear-gradient(135deg, #fef3c7 0%, #fce7f3 50%, #dbeafe 100%);">
   <div class="max-w-lg mx-auto p-4 flex flex-col items-center" style="min-height: 100%;"><!-- Header -->
    <div class="text-center mb-4 mt-4">
     <h1 id="title" class="text-3xl font-bold text-purple-600 drop-shadow-sm">🔗 Word Chain! 🔗</h1>
     <p class="text-sm text-pink-500 mt-1">Connect words by their last letter!</p>
    </div><!-- Score & Chain -->
    <div class="flex gap-4 mb-3">
     <div class="bg-white rounded-2xl px-4 py-2 shadow-md border-2 border-yellow-300 flex items-center gap-2"><span class="text-yellow-500 text-lg">⭐</span> <span class="font-bold text-purple-700" id="score">0</span>
     </div>
     <div class="bg-white rounded-2xl px-4 py-2 shadow-md border-2 border-pink-300 flex items-center gap-2"><span class="text-pink-500 text-lg">🔥</span> <span class="font-bold text-purple-700" id="chain-length">0</span>
     </div>
    </div><!-- Current Letter Prompt -->
    <div id="prompt-area" class="bg-white rounded-3xl p-5 shadow-lg border-3 border-purple-200 w-full text-center mb-4">
     <p class="text-gray-500 text-sm mb-1">Type a word starting with:</p>
     <div id="current-letter" class="text-5xl font-bold text-purple-600 bounce-in">
      Any!
     </div>
    </div><!-- Input -->
    <form id="word-form" class="w-full flex gap-2 mb-4"><input id="word-input" type="text" placeholder="Type your word here..." autocomplete="off" class="flex-1 rounded-2xl px-4 py-3 text-lg border-3 border-purple-300 focus:border-purple-500 focus:outline-none shadow-sm font-medium text-purple-800 placeholder:text-purple-300"> <button type="submit" class="bg-gradient-to-r from-purple-500 to-pink-500 text-white rounded-2xl px-5 py-3 font-bold text-lg shadow-md hover:scale-105 transition-transform active:scale-95">Go!</button>
    </form><!-- Message -->
    <div id="message" class="w-full text-center mb-3 min-h-[2rem] font-medium text-lg"></div><!-- Word Chain Display -->
    <div id="word-chain" class="w-full flex flex-wrap justify-center items-center gap-1 mb-4"></div><!-- Reset --> <button id="reset-btn" class="mt-auto mb-4 bg-white border-2 border-purple-300 text-purple-600 rounded-2xl px-6 py-2 font-bold hover:bg-purple-50 transition-colors flex items-center gap-2"> <i data-lucide="refresh-cw" style="width:18px;height:18px;"></i> New Game </button>
   </div>
  </div>
  <script>
// Simple English word list (common words for primary students)
const VALID_WORDS = new Set([
  "apple","ant","art","arm","air","age","ace","add","ask","ate","away","able","also","area","back","ball","bat","bear","bed","big","bird","blue","boat","book","box","boy","bus","but","buy","cake","call","came","can","car","cat","city","cold","come","cool","cow","cup","cut","dad","day","did","dig","dog","door","down","draw","drop","duck","each","ear","eat","egg","end","even","ever","eye","face","fall","fan","far","fast","fat","fear","feet","felt","few","fill","find","fine","fire","fish","five","flat","fly","food","foot","for","four","fox","free","from","full","fun","game","gate","gave","get","girl","give","glad","goat","goes","gold","gone","good","got","green","grew","grow","gum","had","hair","half","hall","hand","hat","have","head","hear","heat","help","hen","her","here","hero","hide","high","hill","him","his","hit","hold","hole","home","hook","hope","horse","hot","hour","house","how","huge","ice","idea","ill","inch","into","iron","its","jam","jar","jet","job","join","joke","joy","jump","just","keen","keep","kept","key","kick","kid","kind","king","kiss","kite","knee","knew","knot","know","lace","lake","lamp","land","last","late","law","lay","lead","leaf","lean","left","leg","lemon","less","let","lie","life","lift","like","line","lion","lip","list","lit","live","long","look","lord","lost","lot","love","low","luck","made","make","man","many","map","mark","may","mean","meet","men","met","mile","milk","mind","mine","miss","mix","mom","moon","more","most","move","much","mud","mug","must","name","near","neat","neck","need","nest","net","new","next","nice","nine","nod","none","noon","nor","nose","note","now","nut","odd","off","oil","old","once","one","only","open","orange","other","our","out","over","own","page","paid","pain","pair","pan","park","part","pass","past","path","pay","pen","pet","pick","pig","pin","pink","place","plan","plant","play","plus","point","pond","pool","poor","pop","post","pot","pour","pull","push","put","queen","quick","quiet","quit","quite","race","rain","ran","rat","reach","read","real","red","rest","rice","rich","ride","ring","rise","road","rock","rode","roll","roof","room","root","rope","rose","round","row","rub","rule","run","rush","sad","safe","said","sail","salt","same","sand","sang","sat","save","saw","say","sea","seat","seed","seem","seen","self","sell","send","sent","set","seven","shall","shape","she","sheep","ship","shoe","shop","short","shot","show","shut","sick","side","sign","silk","sing","sir","sit","six","size","skin","sky","sleep","slip","slow","small","smell","smile","smoke","snow","soft","soil","sold","some","son","song","soon","sort","soul","south","space","speak","speed","spend","spin","spot","spring","stand","star","start","stay","step","stick","still","stone","stood","stop","store","storm","story","street","strong","such","sugar","summer","sun","sure","sweet","swim","table","tail","take","talk","tall","tape","task","tea","teach","team","tell","ten","tent","term","test","than","thank","that","them","then","there","these","they","thick","thin","thing","think","this","those","three","through","throw","tie","time","tiny","tip","tire","today","toe","told","tone","too","took","tool","top","touch","town","tree","trip","truck","true","trust","try","turn","twelve","twin","two","type","ugly","uncle","under","unit","until","upon","use","used","usual","van","very","view","visit","voice","wait","wake","walk","wall","want","war","warm","wash","watch","water","wave","way","weak","wear","week","well","went","were","west","wet","what","wheel","when","where","which","while","white","who","whole","why","wide","wife","wild","will","win","wind","window","wing","winter","wire","wise","wish","with","without","woke","wolf","woman","won","wood","wool","word","wore","work","world","worm","would","wrap","write","wrong","wrote","yard","year","yellow","yes","yet","you","young","your","zero","zone","zoo"
]);

let usedWords = [];
let score = 0;
let currentLetter = null;

function getLastLetter(word) { return word[word.length - 1].toLowerCase(); }

function updateUI() {
  document.getElementById('score').textContent = score;
  document.getElementById('chain-length').textContent = usedWords.length;

  const letterEl = document.getElementById('current-letter');
  if (currentLetter) {
    letterEl.textContent = currentLetter.toUpperCase();
    letterEl.className = 'text-5xl font-bold text-purple-600 bounce-in';
  } else {
    letterEl.textContent = 'Any!';
    letterEl.className = 'text-5xl font-bold text-purple-600';
  }

  // Render chain
  const chainEl = document.getElementById('word-chain');
  chainEl.innerHTML = '';
  usedWords.forEach((w, i) => {
    const span = document.createElement('span');
    span.className = 'word-bubble bounce-in';
    span.textContent = w;
    chainEl.appendChild(span);
    if (i < usedWords.length - 1) {
      const conn = document.createElement('span');
      conn.className = 'chain-connector';
      conn.textContent = '→';
      chainEl.appendChild(conn);
    }
  });
}

function showMessage(text, type) {
  const el = document.getElementById('message');
  el.textContent = text;
  el.className = 'w-full text-center mb-3 min-h-[2rem] font-medium text-lg ' +
    (type === 'error' ? 'text-red-500 shake' : type === 'success' ? 'text-green-500 bounce-in' : 'text-gray-500');
  if (type === 'error') {
    const input = document.getElementById('word-input');
    input.classList.add('shake');
    setTimeout(() => input.classList.remove('shake'), 400);
  }
}

function submitWord(word) {
  word = word.toLowerCase().trim();

  if (!word) return;
  if (word.length < 2) { showMessage('Too short! Try a longer word 📏', 'error'); return; }
  if (!VALID_WORDS.has(word)) { showMessage('Not a valid word! Try again 🤔', 'error'); return; }
  if (usedWords.includes(word)) { showMessage('Already used! Pick another 🔄', 'error'); return; }
  if (currentLetter && word[0] !== currentLetter) {
    showMessage(`Must start with "${currentLetter.toUpperCase()}" 👀`, 'error'); return;
  }

  usedWords.push(word);
  score += word.length;
  currentLetter = getLastLetter(word);

  const emojis = ['🎉', '✨', '🌟', '💫', '🎊', '👏', '🙌'];
  showMessage(emojis[Math.floor(Math.random() * emojis.length)] + ' Great job!', 'success');
  updateUI();
}

document.getElementById('word-form').addEventListener('submit', (e) => {
  e.preventDefault();
  const input = document.getElementById('word-input');
  submitWord(input.value);
  input.value = '';
  input.focus();
});

document.getElementById('reset-btn').addEventListener('click', () => {
  usedWords = [];
  score = 0;
  currentLetter = null;
  showMessage('New game! Type any word to start 🚀', '');
  updateUI();
});

// Element SDK
const defaultConfig = {
  game_title: 'Word Chain!',
  background_color: '#fef3c7',
  text_color: '#7c3aed',
  primary_action_color: '#a855f7',
  secondary_surface_color: '#ffffff',
  accent_color: '#fbbf24'
};

window.elementSdk.init({
  defaultConfig,
  onConfigChange: async (config) => {
    const title = config.game_title || defaultConfig.game_title;
    document.getElementById('title').textContent = '🔗 ' + title + ' 🔗';
    document.getElementById('title').style.color = config.text_color || defaultConfig.text_color;
    document.getElementById('app').style.background = `linear-gradient(135deg, ${config.background_color || defaultConfig.background_color} 0%, #fce7f3 50%, #dbeafe 100%)`;
  },
  mapToCapabilities: (config) => ({
    recolorables: [
      { get: () => config.background_color || defaultConfig.background_color, set: (v) => { config.background_color = v; window.elementSdk.setConfig({ background_color: v }); } },
      { get: () => config.secondary_surface_color || defaultConfig.secondary_surface_color, set: (v) => { config.secondary_surface_color = v; window.elementSdk.setConfig({ secondary_surface_color: v }); } },
      { get: () => config.text_color || defaultConfig.text_color, set: (v) => { config.text_color = v; window.elementSdk.setConfig({ text_color: v }); } },
      { get: () => config.primary_action_color || defaultConfig.primary_action_color, set: (v) => { config.primary_action_color = v; window.elementSdk.setConfig({ primary_action_color: v }); } },
      { get: () => config.accent_color || defaultConfig.accent_color, set: (v) => { config.accent_color = v; window.elementSdk.setConfig({ accent_color: v }); } }
    ],
    borderables: [],
    fontEditable: {
      get: () => config.font_family || defaultConfig.font_family || 'Fredoka',
      set: (v) => { config.font_family = v; window.elementSdk.setConfig({ font_family: v }); }
    },
    fontSizeable: {
      get: () => config.font_size || 16,
      set: (v) => { config.font_size = v; window.elementSdk.setConfig({ font_size: v }); }
    }
  }),
  mapToEditPanelValues: (config) => new Map([
    ["game_title", config.game_title || defaultConfig.game_title]
  ])
});

updateUI();
lucide.createIcons();
</script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'a06c69896d40d160',t:'MTc4MDYzMzUxNA=='};var a=document.createElement('script');a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
