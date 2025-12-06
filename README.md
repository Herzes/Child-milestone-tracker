# Child-milestone-tracker
Helpful tool for detecting delays in development 
<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Child Milestones Tracker</title>
<script src="/_sdk/element_sdk.js"></script>
<style>
body {
box-sizing: border-box;
margin: 0;
padding: 0;
font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
min-height: 100%;
height: 100%;
width: 100%;
}
html {
height: 100%;
width: 100%;
}

.app-container {
width: 100%;
min-height: 100%;
padding: 40px 20px;
display: flex;
flex-direction: column;
align-items: center;
}

.header {
text-align: center;
margin-bottom: 40px;
color: white;
}

.header h1 {
font-size: 42px;
margin: 0 0 10px 0;
font-weight: 700;
text-shadow: 2px 2px 4px rgba(0,0,0,0.2);
}

.header p {
font-size: 18px;
margin: 0;
opacity: 0.95;
}

.main-card {
background: white;
border-radius: 20px;
box-shadow: 0 20px 60px rgba(0,0,0,0.3);
padding: 40px;
width: 100%;
max-width: 700px;
}

.input-section {
margin-bottom: 30px;
}

.input-section label {
display: block;
font-size: 16px;
font-weight: 600;
margin-bottom: 10px;
color: #333;
}

.input-group {
display: flex;
gap: 15px;
align-items: flex-end;
}

.input-group input {
flex: 1;
padding: 14px 18px;
font-size: 16px;
border: 2px solid #e0e0e0;
border-radius: 10px;
transition: all 0.3s ease;
}

.input-group input:focus {
outline: none;
border-color: #667eea;
box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.input-group button {
padding: 14px 30px;
font-size: 16px;
font-weight: 600;
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
color: white;
border: none;
border-radius: 10px;
cursor: pointer;
transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.input-group button:hover {
transform: translateY(-2px);
box-shadow: 0 10px 25px rgba(102, 126, 234, 0.4);
}

.input-group button:active {
transform: translateY(0);
}

.error-message {
background: #fee;
border-left: 4px solid #e74c3c;
padding: 15px 20px;
border-radius: 8px;
color: #c0392b;
font-weight: 500;
}

.hidden {
display: none;
}

.results-section {
animation: fadeIn 0.5s ease;
}

@keyframes fadeIn {
from {
opacity: 0;
transform: translateY(10px);
}
to {
opacity: 1;
transform: translateY(0);
}
}

.age-display {
font-size: 24px;
font-weight: 700;
color: #667eea;
margin-bottom: 25px;
padding-bottom: 15px;
border-bottom: 3px solid #f0f0f0;
}

.milestone-section {
margin-bottom: 30px;
}

.milestone-section h2 {
font-size: 20px;
font-weight: 700;
color: #333;
margin-bottom: 15px;
display: flex;
align-items: center;
gap: 10px;
}

.milestone-section h2::before {
content: '✓';
background: #4caf50;
color: white;
width: 28px;
height: 28px;
border-radius: 50%;
display: flex;
align-items: center;
justify-content: center;
font-size: 16px;
}

.concerns-section h2::before {
content: '!';
background: #ff9800;
}

.milestone-section ul {
list-style: none;
padding: 0;
margin: 0;
}

.milestone-section li {
padding: 12px 15px;
margin-bottom: 8px;
background: #f8f9fa;
border-radius: 8px;
border-left: 3px solid #4caf50;
color: #555;
line-height: 1.5;
}

.concerns-section li {
border-left-color: #ff9800;
}

@media (max-width: 600px) {
.header h1 {
font-size: 32px;
}

.main-card {
padding: 25px;
}

.input-group {
flex-direction: column;
align-items: stretch;
}

.input-group button {
width: 100%;
}
}
</style>
<style>@view-transition { navigation: auto; }</style>
<script src="/_sdk/data_sdk.js" type="text/javascript"></script>
<script src="https://cdn.tailwindcss.com" type="text/javascript"></script>
</head>
<body>
<div class="app-container">
<header class="header">
<h1 id="app-title">Child Milestones Tracker</h1>
<p id="app-subtitle">Track your child's developmental progress</p>
</header>
<main class="main-card">
<div class="input-section"><label for="age-input" id="input-label">Child's Age (in months)</label>
<div class="input-group"><input type="number" id="age-input" min="0" max="48" placeholder="Enter age (0-48 months)"> <button id="check-button">Check Milestones</button>
</div>
</div>
<div id="error-message" class="error-message hidden">
Please enter a valid age between 0 and 48 months.
</div>
<div id="results" class="results-section hidden">
<div id="age-display" class="age-display"></div>
<div class="milestone-section">
<h2>Expected Milestones</h2>
<ul id="milestones-list"></ul>
</div>
<div id="concerns-section" class="milestone-section concerns-section hidden">
<h2>When to Be Concerned</h2>
<ul id="concerns-list"></ul>
</div>
</div>
</main>
</div>
<script>
// Default configuration
const defaultConfig = {
app_title: 'Child Milestones Tracker',
app_subtitle: 'Track your child\'s developmental progress',
input_label: 'Child\'s Age (in months)',
button_text: 'Check Milestones',
background_color: '#667eea',
surface_color: '#ffffff',
text_color: '#333333',
primary_action_color: '#667eea',
accent_color: '#4caf50',
font_family: 'Segoe UI',
font_size: 16
};

// Milestone data
function getMilestones(months) {
const data = {
milestones: [],
concerns: []
};

if (months <= 2) {
data.milestones = [
'Begins to smile at people',
'Can briefly calm themselves (may bring hands to mouth)',
'Tries to look at parent',
'Coos and makes gurgling sounds',
'Turns head toward sounds',
'Pays attention to faces',
'Begins to follow things with eyes',
'Recognizes people at a distance'
];
data.concerns = [
'Doesn\'t respond to loud sounds',
'Doesn\'t watch things as they move',
'Doesn\'t smile at people',
'Doesn\'t bring hands to mouth',
'Can\'t hold head up when pushing up during tummy time'
];
} else if (months <= 4) {
data.milestones = [
'Smiles spontaneously at people',
'Likes to play with people',
'Copies some movements and facial expressions',
'Begins to babble',
'Babbles with expression and copies sounds',
'Cries in different ways to show hunger, pain, or tiredness',
'Lets you know if they are happy or sad',
'Responds to affection',
'Reaches for toys with one hand',
'Uses hands and eyes together',
'Follows moving things with eyes',
'Holds head steady without support'
];
data.concerns = [
'Doesn\'t watch things as they move',
'Doesn\'t smile at people',
'Can\'t hold head steady',
'Doesn\'t coo or make sounds',
'Doesn\'t bring things to mouth',
'Doesn\'t push down with legs when feet are on hard surface'
];
} else if (months <= 6) {
data.milestones = [
'Knows familiar faces',
'Likes to play with others',
'Responds to other people\'s emotions',
'Likes to look at self in mirror',
'Responds to sounds by making sounds',
'Strings vowels together when babbling',
'Responds to own name',
'Makes sounds to show joy and displeasure',
'Begins to say consonant sounds',
'Looks around at things nearby',
'Brings things to mouth',
'Shows curiosity and tries to get things out of reach',
'Begins to pass things from one hand to another'
];
data.concerns = [
'Doesn\'t try to get things that are in reach',
'Shows no affection for caregivers',
'Doesn\'t respond to sounds around them',
'Has difficulty getting things to mouth',
'Doesn\'t make vowel sounds',
'Doesn\'t roll over in either direction',
'Doesn\'t laugh or make squealing sounds',
'Seems very stiff with tight muscles',
'Seems very floppy, like a rag doll'
];
} else if (months <= 9) {
data.milestones = [
'May be afraid of strangers',
'May be clingy with familiar adults',
'Has favorite toys',
'Understands "no"',
'Makes many different sounds',
'Copies sounds and gestures',
'Uses fingers to point at things',
'Watches the path of something as it falls',
'Looks for things they see you hide',
'Plays peek-a-boo',
'Puts things in mouth',
'Moves things from one hand to the other',
'Picks up things between thumb and index finger',
'Stands holding on',
'Can get into sitting position',
'Sits without support',
'Pulls to stand',
'Crawls'
];
data.concerns = [
'Doesn\'t bear weight on legs with support',
'Doesn\'t sit with help',
'Doesn\'t babble',
'Doesn\'t play any games involving back-and-forth play',
'Doesn\'t respond to own name',
'Doesn\'t seem to recognize familiar people',
'Doesn\'t look where you point',
'Doesn\'t transfer toys from one hand to the other'
];
} else if (months <= 12) {
data.milestones = [
'Is shy or nervous with strangers',
'Cries when parent leaves',
'Has favorite things and people',
'Shows fear in some situations',
'Hands you a book when wanting to hear a story',
'Repeats sounds or actions to get attention',
'Puts out arm or leg to help with dressing',
'Plays games such as peek-a-boo',
'Responds to simple spoken requests',
'Uses simple gestures like shaking head "no"',
'Makes sounds with changes in tone',
'Says "mama" and "dada"',
'Tries to say words you say',
'Explores things in different ways',
'Finds hidden things easily',
'Looks at the right picture when named',
'Copies gestures',
'Starts to use things correctly',
'Bangs two things together',
'Puts things in a container',
'Lets things go without help',
'Pokes with index finger',
'Follows simple directions',
'Gets to sitting position without help',
'Pulls up to stand',
'Walks holding on to furniture',
'May take a few steps without holding on',
'May stand alone'
];
data.concerns = [
'Doesn\'t crawl',
'Can\'t stand when supported',
'Doesn\'t search for things they see you hide',
'Doesn\'t say single words like "mama" or "dada"',
'Doesn\'t learn gestures like waving or shaking head',
'Doesn\'t point to things',
'Loses skills they once had'
];
} else if (months <= 18) {
data.milestones = [
'Likes to hand things to others as play',
'May have temper tantrums',
'May be afraid of strangers',
'Shows affection to familiar people',
'Plays simple pretend games',
'May cling to caregivers in new situations',
'Points to show others something interesting',
'Explores alone but with parent close by',
'Says several single words',
'Says and shakes head "no"',
'Points to show someone what they want',
'Knows what ordinary things are for',
'Points to get the attention of others',
'Shows interest in a doll or stuffed animal',
'Points to one body part',
'Scribbles on own',
'Can follow 1-step verbal commands without gestures',
'Walks alone',
'May walk up steps and run',
'Pulls toys while walking',
'Can help undress themselves',
'Drinks from a cup',
'Eats with a spoon'
];
data.concerns = [
'Doesn\'t point to show things to others',
'Can\'t walk',
'Doesn\'t know what familiar things are for',
'Doesn\'t copy others',
'Doesn\'t gain new words',
'Doesn\'t have at least 6 words',
'Doesn\'t notice or mind when a caregiver leaves or returns',
'Loses skills they once had'
];
} else if (months <= 24) {
data.milestones = [
'Copies others, especially adults and older children',
'Gets excited when with other children',
'Shows more independence',
'Shows defiant behavior',
'Plays mainly beside other children',
'Points to things or pictures when named',
'Knows names of familiar people and body parts',
'Says sentences with 2 to 4 words',
'Follows simple instructions',
'Repeats words overheard in conversation',
'Points to things in a book',
'Finds things even when hidden under covers',
'Begins to sort shapes and colors',
'Completes sentences in familiar books',
'Plays simple make-believe games',
'Builds towers of 4 or more blocks',
'Might use one hand more than the other',
'Follows two-step instructions',
'Names items in a picture book',
'Stands on tiptoe',
'Kicks a ball',
'Begins to run',
'Climbs onto and down from furniture without help',
'Walks up and down stairs holding on',
'Throws ball overhand'
];
data.concerns = [
'Doesn\'t use 2-word phrases',
'Doesn\'t know what to do with common things',
'Doesn\'t copy actions and words',
'Doesn\'t follow simple instructions',
'Doesn\'t walk steadily',
'Loses skills they once had'
];
} else if (months <= 36) {
data.milestones = [
'Copies adults and friends',
'Shows affection for friends without prompting',
'Takes turns in games',
'Shows concern for crying friend',
'Understands "mine," "his," or "hers"',
'Shows a wide range of emotions',
'Separates easily from parents',
'May get upset with major changes in routine',
'Dresses and undresses self',
'Follows instructions with 2 or 3 steps',
'Can name most familiar things',
'Understands words like "in," "on," and "under"',
'Says first name, age, and sex',
'Names a friend',
'Says words like "I," "me," "we," and "you"',
'Speaks well enough for strangers to understand most of the time',
'Carries on a conversation using 2 to 3 sentences',
'Can work toys with buttons, levers, and moving parts',
'Plays make-believe with dolls, animals, and people',
'Does puzzles with 3 or 4 pieces',
'Copies a circle with pencil or crayon',
'Turns book pages one at a time',
'Builds towers of more than 6 blocks',
'Screws and unscrews jar lids',
'Climbs well',
'Runs easily',
'Pedals a tricycle',
'Walks up and down stairs, one foot on each step'
];
data.concerns = [
'Falls down a lot or has trouble with stairs',
'Drools or has very unclear speech',
'Can\'t work simple toys',
'Doesn\'t speak in sentences',
'Doesn\'t understand simple instructions',
'Doesn\'t play pretend or make-believe',
'Doesn\'t want to play with other children or toys',
'Doesn\'t make eye contact',
'Loses skills they once had'
];
} else {
data.milestones = [
'Enjoys doing new things',
'Plays "Mom" and "Dad"',
'Is more and more creative with make-believe play',
'Would rather play with other children than alone',
'Cooperates with other children',
'Often can\'t tell what\'s real and what\'s make-believe',
'Talks about what they like and are interested in',
'Knows some basic rules of grammar',
'Sings a song or says a poem from memory',
'Tells stories',
'Can say first and last name',
'Names some colors and numbers',
'Understands counting',
'Starts to understand time',
'Remembers parts of a story',
'Understands "same" and "different"',
'Draws a person with 2 to 4 body parts',
'Uses scissors',
'Starts to copy some capital letters',
'Plays board or card games',
'Tells you what they think is going to happen next in a book',
'Hops and stands on one foot up to 2 seconds',
'Catches a bounced ball most of the time',
'Pours, cuts with supervision, and mashes own food'
];
data.concerns = [
'Can\'t jump in place',
'Has trouble scribbling',
'Shows no interest in interactive games or make-believe',
'Ignores other children or doesn\'t respond to people outside family',
'Resists dressing, sleeping, using the toilet',
'Can\'t retell a favorite story',
'Doesn\'t follow 3-part commands',
'Doesn\'t understand "same" and "different"',
'Doesn\'t use "me" and "you" correctly',
'Speaks unclearly',
'Loses skills they once had'
];
}

return data;
}

function getAgeDisplay(months) {
if (months === 0) return 'Birth';
if (months === 1) return '1 month';
if (months < 12) return `${months} months`;

const years = Math.floor(months / 12);
const remainingMonths = months % 12;

if (remainingMonths === 0) {
return years === 1 ? '1 year' : `${years} years`;
}

const yearText = years === 1 ? '1 year' : `${years} years`;
const monthText = remainingMonths === 1 ? '1 month' : `${remainingMonths} months`;
return `${yearText} ${monthText}`;
}

function displayResults() {
const ageInput = document.getElementById('age-input');
const resultsDiv = document.getElementById('results');
const errorDiv = document.getElementById('error-message');

const ageMonths = parseInt(ageInput.value);

if (isNaN(ageMonths) || ageMonths < 0 || ageMonths > 48) {
resultsDiv.classList.add('hidden');
errorDiv.classList.remove('hidden');
return;
}

errorDiv.classList.add('hidden');
resultsDiv.classList.remove('hidden');

const data = getMilestones(ageMonths);
const ageDisplay = getAgeDisplay(ageMonths);

document.getElementById('age-display').textContent = `Developmental Check for ${ageMonths} Months (${ageDisplay})`;

// Populate milestones
const milestonesList = document.getElementById('milestones-list');
milestonesList.innerHTML = '';
data.milestones.forEach(milestone => {
const li = document.createElement('li');
li.textContent = milestone;
milestonesList.appendChild(li);
});

// Populate concerns
const concernsList = document.getElementById('concerns-list');
const concernsSection = document.getElementById('concerns-section');
concernsList.innerHTML = '';
if (data.concerns.length > 0) {
concernsSection.classList.remove('hidden');
data.concerns.forEach(concern => {
const li = document.createElement('li');
li.textContent = concern;
concernsList.appendChild(li);
});
} else {
concernsSection.classList.add('hidden');
}
}

// Event listeners
document.getElementById('check-button').addEventListener('click', displayResults);
document.getElementById('age-input').addEventListener('keypress', function(e) {
if (e.key === 'Enter') {
displayResults();
}
});

// Element SDK Integration
async function onConfigChange(config) {
const customFont = config.font_family || defaultConfig.font_family;
const baseSize = config.font_size || defaultConfig.font_size;
const baseFontStack = 'Segoe UI, Tahoma, Geneva, Verdana, sans-serif';

// Update text content
document.getElementById('app-title').textContent = config.app_title || defaultConfig.app_title;
document.getElementById('app-subtitle').textContent = config.app_subtitle || defaultConfig.app_subtitle;
document.getElementById('input-label').textContent = config.input_label || defaultConfig.input_label;
document.getElementById('check-button').textContent = config.button_text || defaultConfig.button_text;

// Update colors
const bgColor = config.background_color || defaultConfig.background_color;
const surfaceColor = config.surface_color || defaultConfig.surface_color;
const textColor = config.text_color || defaultConfig.text_color;
const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
const accentColor = config.accent_color || defaultConfig.accent_color;

document.body.style.background = `linear-gradient(135deg, ${bgColor} 0%, ${primaryColor} 100%)`;
document.querySelector('.main-card').style.backgroundColor = surfaceColor;
document.querySelector('.main-card').style.color = textColor;

const button = document.getElementById('check-button');
button.style.background = `linear-gradient(135deg, ${primaryColor} 0%, ${bgColor} 100%)`;

const labels = document.querySelectorAll('.input-section label, .milestone-section h2, .age-display');
labels.forEach(label => {
label.style.color = textColor;
});

const milestoneItems = document.querySelectorAll('.milestone-section:not(.concerns-section) li');
milestoneItems.forEach(item => {
item.style.borderLeftColor = accentColor;
});

const milestoneHeaders = document.querySelectorAll('.milestone-section:not(.concerns-section) h2::before');
milestoneHeaders.forEach(header => {
header.style.backgroundColor = accentColor;
});

// Update fonts
document.querySelector('.header h1').style.fontFamily = `${customFont}, ${baseFontStack}`;
document.querySelector('.header h1').style.fontSize = `${baseSize * 2.625}px`;

document.querySelector('.header p').style.fontFamily = `${customFont}, ${baseFontStack}`;
document.querySelector('.header p').style.fontSize = `${baseSize * 1.125}px`;

document.querySelector('.age-display').style.fontFamily = `${customFont}, ${baseFontStack}`;
document.querySelector('.age-display').style.fontSize = `${baseSize * 1.5}px`;

const h2Elements = document.querySelectorAll('.milestone-section h2');
h2Elements.forEach(h2 => {
h2.style.fontFamily = `${customFont}, ${baseFontStack}`;
h2.style.fontSize = `${baseSize * 1.25}px`;
});

const allText = document.querySelectorAll('label, input, button, li, p');
allText.forEach(el => {
el.style.fontFamily = `${customFont}, ${baseFontStack}`;
el.style.fontSize = `${baseSize}px`;
});
}

if (window.elementSdk) {
window.elementSdk.init({
defaultConfig: defaultConfig,
onConfigChange: onConfigChange,
mapToCapabilities: (config) => ({
recolorables: [
{
get: () => config.background_color || defaultConfig.background_color,
set: (value) => {
config.background_color = value;
window.elementSdk.setConfig({ background_color: value });
}
},
{
get: () => config.surface_color || defaultConfig.surface_color,
set: (value) => {
config.surface_color = value;
window.elementSdk.setConfig({ surface_color: value });
}
},
{
get: () => config.text_color || defaultConfig.text_color,
set: (value) => {
config.text_color = value;
window.elementSdk.setConfig({ text_color: value });
}
},
{
get: () => config.primary_action_color || defaultConfig.primary_action_color,
set: (value) => {
config.primary_action_color = value;
window.elementSdk.setConfig({ primary_action_color: value });
}
},
{
get: () => config.accent_color || defaultConfig.accent_color,
set: (value) => {
config.accent_color = value;
window.elementSdk.setConfig({ accent_color: value });
}
}
],
borderables: [],
fontEditable: {
get: () => config.font_family || defaultConfig.font_family,
set: (value) => {
config.font_family = value;
window.elementSdk.setConfig({ font_family: value });
}
},
fontSizeable: {
get: () => config.font_size || defaultConfig.font_size,
set: (value) => {
config.font_size = value;
window.elementSdk.setConfig({ font_size: value });
}
}
}),
mapToEditPanelValues: (config) => new Map([
['app_title', config.app_title || defaultConfig.app_title],
['app_subtitle', config.app_subtitle || defaultConfig.app_subtitle],
['input_label', config.input_label || defaultConfig.input_label],
['button_text', config.button_text || defaultConfig.button_text]
])
});
}
</script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a9ba1dfd0cd941b',t:'MTc2NTAyMjUyNS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
