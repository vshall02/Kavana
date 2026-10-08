<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Prescription Reader</title>
<style>
:root{--bg:#f4f8fd;--card:#fff;--ink:#0b1f4d;--muted:#51607a;--brand:#1d63b5;--navy:#042882;--soft:#eaf2fb;--line:#cfe0f5;--ok:#12a39a;--warn:#c47a00;--err:#d93a3a;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#0b1220;--card:#121c31;--ink:#e8eefb;--muted:#9fb0cf;--brand:#5aa9ed;--navy:#0a1d52;--soft:#16233f;--line:#25365c}}
:root[data-theme="dark"]{--bg:#0b1220;--card:#121c31;--ink:#e8eefb;--muted:#9fb0cf;--brand:#5aa9ed;--navy:#0a1d52;--soft:#16233f;--line:#25365c}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font-family:Inter,"Segoe UI",Arial,sans-serif;line-height:1.5}
header{background:linear-gradient(120deg,#042882,#2a83d0);color:#fff;padding:28px 20px 56px}
.wrap{max-width:900px;margin:0 auto}
header h1{margin:0 0 6px;font-size:clamp(26px,5vw,38px)}
header p{margin:0;opacity:.92;max-width:620px}
main{max-width:900px;margin:-34px auto 40px;padding:0 16px}
.card{background:var(--card);border:1px solid var(--line);border-radius:18px;padding:22px;box-shadow:0 10px 30px rgba(4,40,130,.10);margin-bottom:18px}
.drop{border:2px dashed var(--brand);background:var(--soft);border-radius:16px;padding:30px 16px;text-align:center;cursor:pointer;transition:.15s}
.drop.over{background:var(--line)}
.drop svg{width:54px;height:54px;color:var(--brand)}
.drop strong{display:block;font-size:18px;margin-top:6px}
.drop span{color:var(--muted);font-size:14px}
#preview{display:none;max-width:100%;max-height:360px;border-radius:12px;margin:0 auto;border:1px solid var(--line)}
.row{display:flex;gap:10px;flex-wrap:wrap;justify-content:center;margin-top:16px}
button{font:inherit;font-weight:600;border:0;border-radius:999px;padding:12px 26px;cursor:pointer}
.primary{background:var(--brand);color:#fff}.primary:disabled{opacity:.5;cursor:not-allowed}
.ghost{background:transparent;border:1.5px solid var(--brand);color:var(--brand)}
h2{margin:0 0 12px;font-size:20px}
.status{text-align:center;color:var(--muted);padding:6px 0}
.spin{display:inline-block;width:18px;height:18px;border:3px solid var(--line);border-top-color:var(--brand);border-radius:50%;animation:s .8s linear infinite;vertical-align:-3px;margin-right:8px}
@keyframes s{to{transform:rotate(360deg)}}
.med{border:1px solid var(--line);border-left:6px solid var(--brand);border-radius:14px;padding:14px 16px;margin-bottom:12px;background:var(--soft)}
.med h3{margin:0 0 8px;font-size:19px;color:var(--ink)}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));gap:10px}
.k{font-size:12px;text-transform:uppercase;letter-spacing:.06em;color:var(--muted)}
.v{font-weight:600}
.unclear{color:var(--warn)}
.note{font-size:14px;color:var(--muted);margin-top:8px}
.warn{background:rgba(196,122,0,.12);border:1px solid rgba(196,122,0,.45);border-radius:12px;padding:12px 14px;font-size:14px}
.err{color:var(--err);font-weight:600}

.langs{display:flex;flex-wrap:wrap;gap:8px;margin-top:16px}
.langs button{background:rgba(255,255,255,.16);color:#fff;border:1.5px solid rgba(255,255,255,.55);padding:8px 16px;font-size:15px}
.langs button.on{background:#fff;color:#042882}
.chips{display:flex;flex-wrap:wrap;gap:8px;margin-top:6px}
.chip{background:var(--card);border:1.5px solid var(--brand);border-radius:999px;padding:6px 14px;font-weight:600;font-size:15px}
.chip.food{border-color:var(--ok)}
.listen{margin-top:12px}
footer{text-align:center;color:var(--muted);font-size:13px;padding:0 16px 30px}
</style>
</head>
<body>
<header><div class="wrap">
<h1>💊 <span data-t="title">Prescription Reader</span></h1>
<p data-t="sub"></p>
<div class="langs" id="langs" role="group" aria-label="Language"></div>
</div></header>

<main>
<section class="card">
<label class="drop" id="drop" for="file">
<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"><path d="M7 18a4 4 0 0 1-.6-7.96A6 6 0 0 1 18 9.5 4.5 4.5 0 0 1 17.5 18H7z"/><path d="M12 12v6M9.5 14.5 12 12l2.5 2.5"/></svg>
<strong data-t="dropTitle"></strong>
<span data-t="dropSub"></span>
</label>
<input id="file" type="file" accept="image/*" hidden>
<img id="preview" alt="Selected prescription">
<div class="row">
<button class="primary" id="read" disabled data-t="read"></button>
<button class="ghost" id="clear" style="display:none" data-t="clear"></button>
</div>
<div class="status" id="status"></div>
<div class="note" data-t="privacy" style="text-align:center"></div>
</section>

<section class="card" id="resultCard" style="display:none">
<div class="warn" style="margin-bottom:14px;font-weight:600" data-t="warn"></div>
<h2 data-t="details"></h2>
<div id="result"></div>
<button class="ghost listen" id="listen" data-t="listen"></button>
</section>
</main>
<footer>Prescription Reader · web prototype</footer>

<script>
const $=id=>document.getElementById(id);
const L={
en:{n:'English',sp:'en-IN',title:'Prescription Reader',sub:'Upload a photo of a handwritten prescription. AI reads it and shows the medicine name, dosage and timing in a clear format.',dropTitle:'Click to choose or drop a prescription photo',dropSub:'JPG, PNG or WEBP · keep the writing sharp and well lit',read:'Read prescription',clear:'Clear',reading:'Reading prescription…',details:'Medicine details',dosage:'Dosage',timing:'When to take',duration:'For how long',unclear:'Unclear – ask your doctor',warn:'⚠️ AI can misread handwriting. Always confirm the medicine name, dosage and timing with your doctor or pharmacist before taking anything.',doctorNotes:"Doctor's notes",listen:'🔊 Listen',stop:'⏹ Stop',err:'Could not read the prescription. Please try a clearer photo.',noai:'AI reading is available when this page is opened inside Claude.',notimg:'Please choose an image file.',nomed:'No readable medicines found. Please upload a clearer photo.',morning:'Morning',noon:'Afternoon',evening:'Evening',night:'Night',before:'Before food',after:'After food',with:'With food',footer:'Prescription Reader · web prototype'},
hi:{n:'हिन्दी',sp:'hi-IN',title:'पर्ची पढ़ने वाला',sub:'हाथ से लिखी पर्ची की फोटो अपलोड करें। AI उसे पढ़कर दवा का नाम, मात्रा और समय साफ़ तरीके से दिखाएगा।',dropTitle:'पर्ची की फोटो चुनने के लिए यहाँ दबाएँ',dropSub:'JPG, PNG या WEBP · लिखावट साफ़ और रोशनी में हो',read:'पर्ची पढ़ें',clear:'हटाएँ',reading:'पर्ची पढ़ी जा रही है…',details:'दवा की जानकारी',dosage:'मात्रा',timing:'कब लेनी है',duration:'कितने दिन',unclear:'साफ़ नहीं – डॉक्टर से पूछें',warn:'⚠️ AI लिखावट गलत पढ़ सकता है। दवा लेने से पहले नाम, मात्रा और समय डॉक्टर या दवा-दुकानदार (फार्मासिस्ट) से ज़रूर पूछ लें।',doctorNotes:'डॉक्टर की सलाह',listen:'🔊 सुनें',stop:'⏹ रोकें',err:'पर्ची नहीं पढ़ सके। कृपया साफ़ फोटो डालें।',noai:'AI से पढ़ना तभी चलेगा जब यह पेज Claude के अंदर खोला जाए।',notimg:'कृपया फोटो चुनें।',nomed:'कोई दवा पढ़ने में नहीं आई। कृपया साफ़ फोटो डालें।',morning:'सुबह',noon:'दोपहर',evening:'शाम',night:'रात',before:'खाने से पहले',after:'खाने के बाद',with:'खाने के साथ',footer:'पर्ची पढ़ने वाला · वेब प्रोटोटाइप'},
kn:{n:'ಕನ್ನಡ',sp:'kn-IN',title:'ಚೀಟಿ ಓದುಗ',sub:'ಕೈಬರಹದ ಔಷಧಿ ಚೀಟಿಯ ಫೋಟೋ ಅಪ್‌ಲೋಡ್ ಮಾಡಿ. AI ಅದನ್ನು ಓದಿ ಔಷಧಿಯ ಹೆಸರು, ಪ್ರಮಾಣ ಮತ್ತು ಸಮಯವನ್ನು ಸ್ಪಷ್ಟವಾಗಿ ತೋರಿಸುತ್ತದೆ.',dropTitle:'ಚೀಟಿಯ ಫೋಟೋ ಆಯ್ಕೆ ಮಾಡಲು ಇಲ್ಲಿ ಒತ್ತಿ',dropSub:'JPG, PNG ಅಥವಾ WEBP · ಬರಹ ಸ್ಪಷ್ಟವಾಗಿ, ಬೆಳಕಿನಲ್ಲಿರಲಿ',read:'ಚೀಟಿ ಓದಿ',clear:'ತೆಗೆದುಹಾಕಿ',reading:'ಚೀಟಿ ಓದಲಾಗುತ್ತಿದೆ…',details:'ಔಷಧಿಯ ವಿವರಗಳು',dosage:'ಪ್ರಮಾಣ',timing:'ಯಾವಾಗ ತೆಗೆದುಕೊಳ್ಳಬೇಕು',duration:'ಎಷ್ಟು ದಿನ',unclear:'ಸ್ಪಷ್ಟವಿಲ್ಲ – ವೈದ್ಯರನ್ನು ಕೇಳಿ',warn:'⚠️ AI ಕೈಬರಹವನ್ನು ತಪ್ಪಾಗಿ ಓದಬಹುದು. ಔಷಧಿ ತೆಗೆದುಕೊಳ್ಳುವ ಮೊದಲು ಹೆಸರು, ಪ್ರಮಾಣ ಮತ್ತು ಸಮಯವನ್ನು ವೈದ್ಯರು ಅಥವಾ ಔಷಧಿ ಅಂಗಡಿಯವರಿಂದ ಖಚಿತಪಡಿಸಿಕೊಳ್ಳಿ.',doctorNotes:'ವೈದ್ಯರ ಸೂಚನೆ',listen:'🔊 ಕೇಳಿ',stop:'⏹ ನಿಲ್ಲಿಸಿ',err:'ಚೀಟಿ ಓದಲು ಸಾಧ್ಯವಾಗಲಿಲ್ಲ. ಸ್ಪಷ್ಟ ಫೋಟೋ ಹಾಕಿ.',noai:'ಈ ಪುಟವನ್ನು Claude ಒಳಗೆ ತೆರೆದಾಗ ಮಾತ್ರ AI ಓದುತ್ತದೆ.',notimg:'ದಯವಿಟ್ಟು ಫೋಟೋ ಆಯ್ಕೆ ಮಾಡಿ.',nomed:'ಯಾವುದೇ ಔಷಧಿ ಓದಲು ಆಗಲಿಲ್ಲ. ಸ್ಪಷ್ಟ ಫೋಟೋ ಹಾಕಿ.',morning:'ಬೆಳಿಗ್ಗೆ',noon:'ಮಧ್ಯಾಹ್ನ',evening:'ಸಂಜೆ',night:'ರಾತ್ರಿ',before:'ಊಟಕ್ಕೆ ಮೊದಲು',after:'ಊಟದ ನಂತರ',with:'ಊಟದ ಜೊತೆ',footer:'ಚೀಟಿ ಓದುಗ · ವೆಬ್ ಮಾದರಿ'},
ta:{n:'தமிழ்',sp:'ta-IN',title:'மருந்துச் சீட்டு வாசிப்பான்',sub:'கையால் எழுதிய மருந்துச் சீட்டின் புகைப்படத்தை பதிவேற்றுங்கள். AI அதைப் படித்து மருந்தின் பெயர், அளவு, நேரத்தை தெளிவாகக் காட்டும்.',dropTitle:'சீட்டின் புகைப்படத்தைத் தேர்ந்தெடுக்க இங்கே அழுத்தவும்',dropSub:'JPG, PNG அல்லது WEBP · எழுத்து தெளிவாகவும் வெளிச்சத்திலும் இருக்கட்டும்',read:'சீட்டைப் படி',clear:'நீக்கு',reading:'சீட்டு படிக்கப்படுகிறது…',details:'மருந்து விவரங்கள்',dosage:'அளவு',timing:'எப்போது எடுக்க வேண்டும்',duration:'எத்தனை நாள்',unclear:'தெளிவாக இல்லை – மருத்துவரிடம் கேளுங்கள்',warn:'⚠️ AI கையெழுத்தை தவறாகப் படிக்கக்கூடும். மருந்து சாப்பிடும் முன் பெயர், அளவு, நேரத்தை மருத்துவர் அல்லது மருந்தாளரிடம் உறுதிசெய்து கொள்ளுங்கள்.',doctorNotes:'மருத்துவரின் குறிப்பு',listen:'🔊 கேளுங்கள்',stop:'⏹ நிறுத்து',err:'சீட்டைப் படிக்க முடியவில்லை. தெளிவான புகைப்படம் தரவும்.',noai:'இந்தப் பக்கம் Claude உள்ளே திறக்கப்படும்போது மட்டுமே AI படிக்கும்.',notimg:'புகைப்படத்தைத் தேர்ந்தெடுக்கவும்.',nomed:'எந்த மருந்தையும் படிக்க முடியவில்லை. தெளிவான புகைப்படம் தரவும்.',morning:'காலை',noon:'மதியம்',evening:'மாலை',night:'இரவு',before:'உணவுக்கு முன்',after:'உணவுக்குப் பின்',with:'உணவுடன்',footer:'மருந்துச் சீட்டு வாசிப்பான் · வலை முன்மாதிரி'},
te:{n:'తెలుగు',sp:'te-IN',title:'చీటీ రీడర్',sub:'చేతిరాత మందుల చీటీ ఫోటోను అప్‌లోడ్ చేయండి. AI దాన్ని చదివి మందు పేరు, మోతాదు, సమయాన్ని స్పష్టంగా చూపిస్తుంది.',dropTitle:'చీటీ ఫోటోను ఎంచుకోవడానికి ఇక్కడ నొక్కండి',dropSub:'JPG, PNG లేదా WEBP · రాత స్పష్టంగా, వెలుతురులో ఉండాలి',read:'చీటీ చదవండి',clear:'తొలగించు',reading:'చీటీ చదువుతోంది…',details:'మందుల వివరాలు',dosage:'మోతాదు',timing:'ఎప్పుడు వేసుకోవాలి',duration:'ఎన్ని రోజులు',unclear:'స్పష్టంగా లేదు – డాక్టర్‌ను అడగండి',warn:'⚠️ AI చేతిరాతను తప్పుగా చదవవచ్చు. మందు వేసుకునే ముందు పేరు, మోతాదు, సమయాన్ని డాక్టర్ లేదా ఫార్మసిస్ట్‌తో నిర్ధారించుకోండి.',doctorNotes:'డాక్టర్ సూచన',listen:'🔊 వినండి',stop:'⏹ ఆపు',err:'చీటీని చదవలేకపోయింది. స్పష్టమైన ఫోటో ఇవ్వండి.',noai:'ఈ పేజీని Claude లోపల తెరిచినప్పుడే AI చదువుతుంది.',notimg:'దయచేసి ఫోటోను ఎంచుకోండి.',nomed:'ఏ మందూ చదవలేకపోయింది. స్పష్టమైన ఫోటో ఇవ్వండి.',morning:'ఉదయం',noon:'మధ్యాహ్నం',evening:'సాయంత్రం',night:'రాత్రి',before:'భోజనానికి ముందు',after:'భోజనం తర్వాత',with:'భోజనంతో',footer:'చీటీ రీడర్ · వెబ్ నమూనా'},
ml:{n:'മലയാളം',sp:'ml-IN',title:'കുറിപ്പടി വായന',sub:'കൈയെഴുത്ത് കുറിപ്പടിയുടെ ഫോട്ടോ അപ്‌ലോഡ് ചെയ്യുക. AI അത് വായിച്ച് മരുന്നിന്റെ പേര്, അളവ്, സമയം എന്നിവ വ്യക്തമായി കാണിക്കും.',dropTitle:'കുറിപ്പടിയുടെ ഫോട്ടോ തിരഞ്ഞെടുക്കാൻ ഇവിടെ അമർത്തുക',dropSub:'JPG, PNG അല്ലെങ്കിൽ WEBP · എഴുത്ത് വ്യക്തമായും വെളിച്ചത്തിലും ആയിരിക്കണം',read:'കുറിപ്പടി വായിക്കുക',clear:'നീക്കുക',reading:'കുറിപ്പടി വായിക്കുന്നു…',details:'മരുന്നിന്റെ വിവരങ്ങൾ',dosage:'അളവ്',timing:'എപ്പോൾ കഴിക്കണം',duration:'എത്ര ദിവസം',unclear:'വ്യക്തമല്ല – ഡോക്ടറോട് ചോദിക്കുക',warn:'⚠️ AI കൈയക്ഷരം തെറ്റായി വായിച്ചേക്കാം. മരുന്ന് കഴിക്കുന്നതിന് മുമ്പ് പേര്, അളവ്, സമയം എന്നിവ ഡോക്ടറോടോ ഫാർമസിസ്റ്റിനോടോ ഉറപ്പാക്കുക.',doctorNotes:'ഡോക്ടറുടെ നിർദേശം',listen:'🔊 കേൾക്കുക',stop:'⏹ നിർത്തുക',err:'കുറിപ്പടി വായിക്കാൻ കഴിഞ്ഞില്ല. വ്യക്തമായ ഫോട്ടോ നൽകുക.',noai:'ഈ പേജ് Claude-നുള്ളിൽ തുറന്നാൽ മാത്രമേ AI വായിക്കൂ.',notimg:'ദയവായി ഫോട്ടോ തിരഞ്ഞെടുക്കുക.',nomed:'ഒരു മരുന്നും വായിക്കാൻ കഴിഞ്ഞില്ല. വ്യക്തമായ ഫോട്ടോ നൽകുക.',morning:'രാവിലെ',noon:'ഉച്ചയ്ക്ക്',evening:'വൈകുന്നേരം',night:'രാത്രി',before:'ഭക്ഷണത്തിന് മുമ്പ്',after:'ഭക്ഷണത്തിന് ശേഷം',with:'ഭക്ഷണത്തോടൊപ്പം',footer:'കുറിപ്പടി വായന · വെബ് മാതൃക'}
};
L.en.privacy='🔒 Your photo is sent to an AI to be read. Avoid sharing photos with personal details you are not comfortable sharing.';L.hi.privacy='🔒 आपकी फोटो पढ़ने के लिए AI को भेजी जाती है। जिन निजी जानकारियों को आप साझा नहीं करना चाहते, उनकी फोटो न डालें।';L.kn.privacy='🔒 ನಿಮ್ಮ ಫೋಟೋವನ್ನು ಓದಲು AI ಗೆ ಕಳುಹಿಸಲಾಗುತ್ತದೆ. ಹಂಚಿಕೊಳ್ಳಲು ಇಷ್ಟವಿಲ್ಲದ ವೈಯಕ್ತಿಕ ವಿವರಗಳಿರುವ ಫೋಟೋ ಹಾಕಬೇಡಿ.';L.ta.privacy='🔒 உங்கள் புகைப்படம் படிப்பதற்காக AI-க்கு அனுப்பப்படும். பகிர விரும்பாத தனிப்பட்ட விவரங்கள் உள்ள புகைப்படங்களைத் தவிர்க்கவும்.';L.te.privacy='🔒 మీ ఫోటో చదవడానికి AI కి పంపబడుతుంది. పంచుకోవడానికి ఇష్టం లేని వ్యక్తిగత వివరాలున్న ఫోటోలను ఇవ్వకండి.';L.ml.privacy='🔒 നിങ്ങളുടെ ഫോട്ടോ വായിക്കാൻ AI-ക്ക് അയയ്ക്കുന്നു. പങ്കിടാൻ ആഗ്രഹിക്കാത്ത വ്യക്തിഗത വിവരങ്ങളുള്ള ഫോട്ടോ നൽകരുത്.';
const EN_NAMES={en:'English',hi:'Hindi',kn:'Kannada',ta:'Tamil',te:'Telugu',ml:'Malayalam'};
const ICON={morning:'🌅',noon:'☀️',evening:'🌇',night:'🌙'};
let lang='en',blob=null,sample=null,last=null,speaking=false;
try{const sv=localStorage.getItem('rr_lang');if(sv&&L[sv])lang=sv;else{const b=(navigator.language||'en').slice(0,2);if(L[b])lang=b}}catch(e){}
const t=k=>(L[lang][k]||L.en[k]);
function esc(x){const d=document.createElement('div');d.textContent=x==null?'':String(x);return d.innerHTML}
function val(v){const x=(v||'').toString().trim();return (!x||/^unclear$/i.test(x))?'<span class="unclear">'+esc(t('unclear'))+'</span>':esc(x)}

function applyLang(){
 document.documentElement.lang=lang;
 document.querySelectorAll('[data-t]').forEach(el=>{el.textContent=t(el.dataset.t)});
 document.querySelector('footer').textContent=t('footer');
 const bar=$('langs');bar.innerHTML='';
 Object.keys(L).forEach(k=>{const b=document.createElement('button');b.textContent=L[k].n;b.className=k===lang?'on':'';b.setAttribute('aria-pressed',k===lang);
  b.onclick=()=>{if(k===lang)return;stopSpeak();lang=k;try{localStorage.setItem('rr_lang',k)}catch(e){}last=null;$('resultCard').style.display='none';$('status').textContent='';applyLang()};bar.appendChild(b)});
 if(!sample&&sampleChecked)$('status').textContent=t('noai');
}
let sampleChecked=false;
(async()=>{try{sample=await claude.use("sample")}catch(e){sample=null}sampleChecked=true;if(!sample)$('status').textContent=t('noai')})();
applyLang();

function setFile(f){
 if(!f||!f.type.startsWith('image/')){$('status').innerHTML='<span class="err">'+esc(t('notimg'))+'</span>';return}
 blob=f;const p=$('preview');p.src=URL.createObjectURL(f);p.style.display='block';
 $('drop').style.display='none';$('read').disabled=false;$('clear').style.display='inline-block';
 $('status').textContent='';$('resultCard').style.display='none';
}
$('file').addEventListener('change',e=>setFile(e.target.files[0]));
['dragover','dragenter'].forEach(ev=>$('drop').addEventListener(ev,e=>{e.preventDefault();$('drop').classList.add('over')}));
['dragleave','drop'].forEach(ev=>$('drop').addEventListener(ev,e=>{e.preventDefault();$('drop').classList.remove('over')}));
$('drop').addEventListener('drop',e=>{setFile(e.dataTransfer.files[0])});
$('clear').onclick=()=>{stopSpeak();blob=null;last=null;$('file').value='';$('preview').style.display='none';$('drop').style.display='block';$('read').disabled=true;$('clear').style.display='none';$('resultCard').style.display='none';$('status').textContent=''};

async function shrink(f){
 const img=await createImageBitmap(f);const max=1600;const sc=Math.min(1,max/Math.max(img.width,img.height));
 const c=document.createElement('canvas');c.width=Math.round(img.width*sc);c.height=Math.round(img.height*sc);
 c.getContext('2d').drawImage(img,0,0,c.width,c.height);
 return await new Promise(r=>c.toBlob(r,'image/jpeg',0.9));
}

$('read').onclick=async()=>{
 if(!blob)return;
 if(!sample){$('status').innerHTML='<span class="err">'+esc(t('noai'))+'</span>';return}
 stopSpeak();$('read').disabled=true;$('resultCard').style.display='none';
 $('status').innerHTML='<span class="spin"></span>'+esc(t('reading'));
 try{
  const img=await shrink(blob);const LN=EN_NAMES[lang];
  const prompt=`You are helping a patient, possibly with little education, read a handwritten medical prescription from the attached photo.
Write all explanatory text in simple, everyday ${LN}.
Return ONLY JSON in this exact shape:
{"readable":true|false,"medicines":[{"name":"medicine name as written","name_local":"the same name written in ${LN} script (or the same if ${LN} is English)","dosage":"dose and form in ${LN}, e.g. 1 tablet","times":["morning","noon","evening","night"],"food":"before|after|with|any|unclear","duration":"for how many days, in ${LN}, or unclear","notes":"any extra instruction in ${LN}, or empty"}],"doctor_notes":"in ${LN}, or empty","message":"short note in ${LN} if not readable"}
Rules: read only what is written; never guess or invent. "times" lists only the times of day the prescription clearly says (1-0-1 means morning and night; 1-1-1 means morning, noon and night; use [] if unclear). Use "unclear" for anything you cannot read with confidence. If the image is not a prescription or is unreadable, set readable=false.`;
  const data=await sample.json(prompt,{images:[img],modelTier:'default',cache:false});
  last=data;render(data);$('status').textContent='';
 }catch(e){
  const m={not_granted:'🚫',rate_limited:'⏳'};
  $('status').innerHTML='<span class="err">'+esc((m[e&&e.code]||'')+' '+t('err'))+'</span>';
 }finally{$('read').disabled=false}
};

function chips(m){
 const out=[];
 (Array.isArray(m.times)?m.times:[]).forEach(k=>{if(ICON[k])out.push(`<span class="chip">${ICON[k]} ${esc(t(k))}</span>`)});
 const f={before:'before',after:'after',with:'with'}[m.food];
 if(f)out.push(`<span class="chip food">🍽️ ${esc(t(f))}</span>`);
 return out.join('');
}
function render(d){
 const box=$('result');box.innerHTML='';
 if(!d||d.readable===false||!Array.isArray(d.medicines)||!d.medicines.length){
  box.innerHTML='<p>'+esc((d&&d.message)||t('nomed'))+'</p>';$('listen').style.display='none';
 }else{
  $('listen').style.display='inline-block';
  d.medicines.forEach((m,i)=>{
   const loc=(m.name_local||'').trim();const nm=(m.name||'').trim();
   const title=nm&&loc&&loc.toLowerCase()!==nm.toLowerCase()?`${esc(nm)} <span style="font-weight:500;color:var(--muted)">(${esc(loc)})</span>`:val(nm||loc);
   const c=chips(m);
   box.insertAdjacentHTML('beforeend',`<div class="med"><h3>${i+1}. ${title}</h3><div class="grid">
   <div><div class="k">${esc(t('dosage'))}</div><div class="v">${val(m.dosage)}</div></div>
   <div><div class="k">${esc(t('duration'))}</div><div class="v">${val(m.duration)}</div></div></div>
   <div style="margin-top:10px"><div class="k">${esc(t('timing'))}</div>${c?`<div class="chips">${c}</div>`:`<div class="v">${val('')}</div>`}</div>
   ${m.notes&&!/^unclear$/i.test(m.notes)?`<div class="note">${esc(m.notes)}</div>`:''}</div>`);
  });
  if(d.doctor_notes&&!/^unclear$/i.test(d.doctor_notes))box.insertAdjacentHTML('beforeend',`<div class="note"><strong>${esc(t('doctorNotes'))}:</strong> ${esc(d.doctor_notes)}</div>`);
 }
 $('resultCard').style.display='block';$('resultCard').scrollIntoView({behavior:'smooth',block:'start'});
}

/* read aloud */
function spokenText(){
 if(!last||!Array.isArray(last.medicines))return '';
 return last.medicines.map((m,i)=>{
  const parts=[(m.name_local||m.name||'').trim()];
  if(m.dosage&&!/^unclear$/i.test(m.dosage))parts.push(m.dosage);
  (m.times||[]).forEach(k=>{if(ICON[k])parts.push(t(k))});
  const f={before:'before',after:'after',with:'with'}[m.food];if(f)parts.push(t(f));
  if(m.duration&&!/^unclear$/i.test(m.duration))parts.push(m.duration);
  return parts.filter(Boolean).join(', ')+'.';
 }).join(' ');
}
function stopSpeak(){try{speechSynthesis.cancel()}catch(e){}speaking=false;$('listen').textContent=t('listen')}
$('listen').onclick=()=>{
 if(speaking){stopSpeak();return}
 try{
  const u=new SpeechSynthesisUtterance(spokenText());u.lang=L[lang].sp;u.rate=0.9;
  u.onend=u.onerror=()=>{speaking=false;$('listen').textContent=t('listen')};
  speaking=true;$('listen').textContent=t('stop');speechSynthesis.cancel();speechSynthesis.speak(u);
 }catch(e){speaking=false}
};
</script>
</body>
</html>
