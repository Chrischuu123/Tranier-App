# Tranier-App
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Muskelgruppe</title>
<style>
* {
    box-sizing: border-box;
}
body {
    margin: 0;
    background: #0d0f12;
    color: white;
    font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", Arial, sans-serif;
}
.container {
    max-width: 700px;
    margin: auto;
    padding: 25px 16px 40px;
}
h1 {
    font-size: 34px;
    margin-bottom: 25px;
}
h2 {
    font-size: 28px;
}
.section {
    color: #999;
    font-size: 13px;
    text-transform: uppercase;
    margin: 25px 5px 10px;
    letter-spacing: 1px;
}
.card {
    background: #191c21;
    border-radius: 18px;
    overflow: hidden;
    border: 1px solid #292d35;
}
button {
    font-family: inherit;
}
.muscle {
    width: 100%;
    padding: 20px 16px;
    background: none;
    border: none;
    border-bottom: 1px solid #292d35;
    color: white;
    text-align: left;
    font-size: 19px;
    font-weight: 600;
}
.muscle:last-child {
    border-bottom: none;
}
.arrow {
    float: right;
    color: #777;
    font-size: 24px;
}
.exercise {
    width: 100%;
    min-height: 70px;
    padding: 17px 15px;
    background: none;
    border: none;
    border-bottom: 1px solid #292d35;
    color: white;
    text-align: left;
    font-size: 17px;
}
.exercise:last-child {
    border-bottom: none;
}
.weight {
    float: right;
    color: #aaa;
    font-size: 14px;
    text-align: right;
}
.add {
    width: 100%;
    margin-top: 12px;
    padding: 15px;
    background: #292e38;
    color: white;
    border: none;
    border-radius: 14px;
    font-size: 16px;
    font-weight: bold;
}
.back {
    padding: 10px 14px;
    background: #292e38;
    border: none;
    border-radius: 12px;
    color: white;
    font-weight: bold;
    margin-bottom: 10px;
}
.settings {
    padding: 18px;
    width: 100%;
    background: none;
    border: none;
    color: white;
    text-align: left;
    font-size: 17px;
}
.modal {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,.7);
    align-items: flex-end;
}
.modal.active {
    display: flex;
}
.modal-box {
    width: 100%;
    max-width: 700px;
    margin: auto;
    background: #191c21;
    border-radius: 25px 25px 0 0;
    padding: 22px 18px 35px;
}
label {
    display: block;
    margin-top: 15px;
    margin-bottom: 6px;
    color: #aaa;
}
input,
select {
    width: 100%;
    padding: 14px;
    background: #0d0f12;
    border: 1px solid #363b45;
    border-radius: 12px;
    color: white;
    font-size: 17px;
}
.actions {
    display: flex;
    gap: 10px;
    margin-top: 18px;
}
.actions button {
    flex: 1;
    padding: 14px;
    border: none;
    border-radius: 12px;
    font-weight: bold;
}
.save {
    background: white;
    color: black;
}
.cancel {
    background: #292e38;
    color: white;
}
.delete {
    background: #512128;
    color: #ffb8c0;
}
.info {
    color: #999;
    font-size: 14px;
}
</style>
</head>
<body>
<div class="container" id="app"></div>
<!-- Übung bearbeiten -->
<div class="modal" id="exerciseModal">
    <div class="modal-box">
        <h2>Übung bearbeiten</h2>
        <label>Übungsname</label>
        <input id="exerciseName">
        <label>Gewicht</label>
        <input id="exerciseWeight" type="number" step="0.5">
        <label>Gewichtsart</label>
        <select id="exerciseType">
            <option value="hand">kg/Hand</option>
            <option value="bar">kg (Langhantel)</option>
            <option value="body">Körpergewicht</option>
        </select>
        <p class="info" id="bodyInfo"></p>
        <div class="actions">
            <button class="cancel" onclick="closeModal()">Abbrechen</button>
            <button class="save" onclick="saveExercise()">Speichern</button>
        </div>
        <div class="actions">
            <button class="delete" onclick="deleteExercise()">
                Übung entfernen
            </button>
        </div>
    </div>
</div>
<!-- Körpergewicht -->
<div class="modal" id="bodyModal">
    <div class="modal-box">
        <h2>Körpergewicht</h2>
        <label>Dein Körpergewicht</label>
        <input id="bodyWeight" type="number" step="0.1">
        <div class="actions">
            <button class="cancel" onclick="closeBodyModal()">
                Abbrechen
            </button>
            <button class="save" onclick="saveBodyWeight()">
                Speichern
            </button>
        </div>
    </div>
</div>
<script>
/* =========================
   MUSKELGRUPPEN
========================= */
const groups = [
    "Brust",
    "Schulter",
    "Bizeps",
    "Trizeps",
    "Rücken"
];
/* =========================
   STANDARDÜBUNGEN
========================= */
const standardExercises = {
    "Brust": [
        ["Bankdrücken", 60, "bar"],
        ["Enges Bankdrücken", 50, "bar"],
        ["Flies", 15, "hand"]
    ],
    "Schulter": [
        ["Front-Flies", 10, "hand"],
        ["Flies", 10, "hand"],
        ["Reversed Flies", 10, "hand"]
    ],
    "Bizeps": [
        ["Schrägbank-Curls", 12, "hand"],
        ["Curls", 12, "hand"]
    ],
    "Trizeps": [
        ["Vorgebeugte Curls", 15, "hand"],
        ["Curls liegend", 30, "bar"]
    ],
    "Rücken": [
        ["Klimmzüge", 0, "body"],
        ["Rudern breit", 40, "bar"],
        ["Rudern eng", 40, "bar"]
    ]
};
/* =========================
   DATEN LADEN
========================= */
let exercises =
    JSON.parse(localStorage.getItem("muskelgruppeExercises"));
let bodyWeight =
    Number(localStorage.getItem("muskelgruppeBodyWeight")) || 70;
let currentGroup = null;
let currentExercise = null;
/* =========================
   ERSTE DATEN
========================= */
if (!exercises) {
    exercises = {};
    groups.forEach(group => {
        exercises[group] =
            standardExercises[group].map(item => ({
                name: item[0],
                weight: item[1],
                type: item[2]
            }));
    });
    saveData();
}
/* =========================
   SPEICHERN
========================= */
function saveData() {
    localStorage.setItem(
        "muskelgruppeExercises",
        JSON.stringify(exercises)
    );
    localStorage.setItem(
        "muskelgruppeBodyWeight",
        bodyWeight
    );
}
/* =========================
   STARTSEITE
========================= */
function showHome() {
    currentGroup = null;
    let html = `
        <h1>Muskelgruppe</h1>
        <div class="section">
            Muskelgruppen
        </div>
        <div class="card">
    `;
    groups.forEach(group => {
        html += `
            <button
                class="muscle"
                onclick="showGroup('${group}')">
                ${group}
                <span class="arrow">›</span>
            </button>
        `;
    });
    html += `
        </div>
        <div class="section">
            Einstellungen
        </div>
        <div class="card">
            <button
                class="settings"
                onclick="openBodyModal()">
                Körpergewicht
                <span class="arrow">
                    ${format(bodyWeight)} kg ›
                </span>
            </button>
        </div>
    `;
    document.getElementById("app").innerHTML = html;
}
/* =========================
   MUSKELGRUPPE
========================= */
function showGroup(group) {
    currentGroup = group;
    let html = `
        <button
            class="back"
            onclick="showHome()">
            ‹ Muskelgruppen
        </button>
        <h1>${group}</h1>
        <div class="section">
            Übungen
        </div>
        <div class="card">
    `;
    if (exercises[group].length === 0) {
        html += `
            <p style="padding:20px;color:#999;text-align:center">
                Noch keine Übungen
            </p>
        `;
    }
    exercises[group].forEach((exercise, index) => {
        html += `
            <button
                class="exercise"
                onclick="editExercise(${index})">
                ${exercise.name}
                <span class="weight">
                    ${displayWeight(exercise)}
                </span>
            </button>
        `;
    });
    html += `
        </div>
        <button
            class="add"
            onclick="addExercise()">
            ＋ Übung hinzufügen
        </button>
    `;
    document.getElementById("app").innerHTML = html;
}
/* =========================
   GEWICHT ANZEIGEN
========================= */
function displayWeight(exercise) {
    if (exercise.type === "body") {
        return `${format(bodyWeight)} kg (Körpergewicht)`;
    }
    if (exercise.type === "hand") {
        return `${format(exercise.weight)} kg/Hand`;
    }
    return `${format(exercise.weight)} kg (Langhantel)`;
}
/* =========================
   ÜBUNG BEARBEITEN
========================= */
function editExercise(index) {
    currentExercise = index;
    const exercise =
        exercises[currentGroup][index];
    document.getElementById("exerciseName").value =
        exercise.name;
    document.getElementById("exerciseWeight").value =
        exercise.weight;
    document.getElementById("exerciseType").value =
        exercise.type;
    updateBodyInfo();
    document
        .getElementById("exerciseModal")
        .classList.add("active");
}
/* =========================
   NEUE ÜBUNG
========================= */
function addExercise() {
    currentExercise = null;
    document.getElementById("exerciseName").value =
        "Neue Übung";
    document.getElementById("exerciseWeight").value =
        0;
    document.getElementById("exerciseType").value =
        "hand";
    updateBodyInfo();
    document
        .getElementById("exerciseModal")
        .classList.add("active");
}
/* =========================
   SPEICHERN
========================= */
function saveExercise() {
    const name =
        document.getElementById("exerciseName")
        .value.trim() || "Neue Übung";
    const weight =
        Number(
            document.getElementById("exerciseWeight")
            .value
        ) || 0;
    const type =
        document.getElementById("exerciseType")
        .value;
    const exercise = {
        name: name,
        weight: weight,
        type: type
    };
    if (currentExercise === null) {
        exercises[currentGroup].push(exercise);
    } else {
        exercises[currentGroup][currentExercise] =
            exercise;
    }
    saveData();
    closeModal();
    showGroup(currentGroup);
}
/* =========================
   ÜBUNG LÖSCHEN
========================= */
function deleteExercise() {
    if (currentExercise === null) {
        return;
    }
    exercises[currentGroup]
        .splice(currentExercise, 1);
    saveData();
    closeModal();
    showGroup(currentGroup);
}
/* =========================
   MODAL SCHLIESSEN
========================= */
function closeModal() {
    document
        .getElementById("exerciseModal")
        .classList.remove("active");
}
/* =========================
   KÖRPERGEWICHT
========================= */
function openBodyModal() {
    document.getElementById("bodyWeight").value =
        bodyWeight;
    document
        .getElementById("bodyModal")
        .classList.add("active");
}
function closeBodyModal() {
    document
        .getElementById("bodyModal")
        .classList.remove("active");
}
function saveBodyWeight() {
    bodyWeight =
        Number(
            document.getElementById("bodyWeight")
            .value
        ) || 0;
    saveData();
    closeBodyModal();
    showHome();
}
/* =========================
   KÖRPERGEWICHT INFO
========================= */
function updateBodyInfo() {
    const type =
        document.getElementById("exerciseType")
        .value;
    const info =
        document.getElementById("bodyInfo");
    if (type === "body") {
        info.innerText =
            `Aktuelles Körpergewicht: ${format(bodyWeight)} kg`;
    } else {
        info.innerText = "";
    }
}
document
    .getElementById("exerciseType")
    .addEventListener(
        "change",
        updateBodyInfo
    );
/* =========================
   ZAHL FORMATIEREN
========================= */
function format(number) {
    return Number(number)
        .toLocaleString(
            "de-DE",
            {
                maximumFractionDigits: 1
            }
        );
}
/* =========================
   START
========================= */
showHome();
</script>
</body>
</html>