<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alphabet Matrix Game</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            color: #fff;
            text-align: center;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 20px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        p {
            font-size: 1.2rem;
            margin-bottom: 20px;
        }

        .matrix {
            display: inline-block;
            background: rgba(255, 255, 255, 0.1);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            margin: 10px;
        }

        .matrix div {
            margin: 5px;
            font-size: 1.5rem;
            font-weight: bold;
        }

        input {
            padding: 10px;
            margin: 10px;
            border: none;
            border-radius: 5px;
            font-size: 1rem;
            width: 80px;
            text-align: center;
        }

        button {
            padding: 10px 20px;
            margin: 10px;
            border: none;
            border-radius: 5px;
            background: #ff6f61;
            color: #fff;
            font-size: 1rem;
            cursor: pointer;
            transition: background 0.3s ease;
        }

        button:hover {
            background: #ff3b2f;
        }

        .container {
            background: rgba(255, 255, 255, 0.1);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            margin: 10px;
            width: 80%;
            max-width: 500px;
        }

        .hidden {
            display: none;
        }

        .fade-in {
            animation: fadeIn 0.5s ease-in-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>
    <h1>Alphabet Matrix Game</h1>
    <p>Select the column numbers to reveal the hidden name!</p>

    <!-- Step 1: Display the alphabet matrix -->
    <div id="matrix-container" class="container fade-in">
        <div class="matrix" id="initial-matrix"></div>
    </div>

    <!-- Step 2: Input for number of letters -->
    <div id="num-letters-container" class="container fade-in">
        <label for="num-letters">How many letters are in the name?</label>
        <input type="number" id="num-letters" min="1" max="10">
        <button onclick="startGame()">Start</button>
    </div>

    <!-- Step 3: Input for column numbers -->
    <div id="column-input-container" class="container hidden">
        <p>Enter the column numbers for each letter:</p>
        <div id="column-inputs"></div>
        <button onclick="submitColumns()">Next</button>
    </div>

    <!-- Step 4: Display transposed matrix -->
    <div id="transposed-matrix-container" class="container hidden">
        <p>Select the column numbers again:</p>
        <div class="matrix" id="transposed-matrix"></div>
        <div id="final-column-inputs"></div>
        <button onclick="submitFinalColumns()">Reveal Name</button>
    </div>

    <!-- Step 5: Display final result -->
    <div id="result-container" class="container hidden">
        <h2>Final Mappings:</h2>
        <div id="final-mappings"></div>
        <h2>The name you're thinking of is:</h2>
        <div id="final-name" style="font-size: 2rem; font-weight: bold; color: #ff6f61;"></div>
    </div>

    <script>
        // JavaScript code will go here
    </script>
</body>
</html>

<script>
    const alphabet = "abcdefghijklmnopqrstuvwxyz";
    const matrix = [];
    let numLetters = 0;
    let columnNumbers = [];
    let transposedRows = [];
    let finalMappings = [];
    let finalName = "";

    // Step 1: Create the 6x5 alphabet matrix
    function createMatrix() {
        for (let i = 0; i < alphabet.length; i += 5) {
            matrix.push(alphabet.slice(i, i + 5));
        }
        displayMatrix(matrix, "initial-matrix");
    }

    // Step 2: Display the matrix
    function displayMatrix(matrix, containerId) {
        const container = document.getElementById(containerId);
        container.innerHTML = "";
        matrix.forEach(row => {
            const rowDiv = document.createElement("div");
            rowDiv.textContent = row.split("").join(" | ");
            container.appendChild(rowDiv);
        });
    }

    // Step 3: Start the game
    function startGame() {
        numLetters = parseInt(document.getElementById("num-letters").value);
        if (numLetters < 1 || numLetters > 10) {
            alert("Please enter a number between 1 and 10.");
            return;
        }
        document.getElementById("num-letters-container").classList.add("hidden");
        document.getElementById("column-input-container").classList.remove("hidden");
        document.getElementById("column-input-container").classList.add("fade-in");

        // Create input fields for column numbers
        const columnInputs = document.getElementById("column-inputs");
        columnInputs.innerHTML = "";
        for (let i = 0; i < numLetters; i++) {
            const label = document.createElement("label");
            label.textContent = `Column for letter ${i + 1}: `;
            const input = document.createElement("input");
            input.type = "number";
            input.min = 1;
            input.max = 5;
            input.required = true;
            columnInputs.appendChild(label);
            columnInputs.appendChild(input);
            columnInputs.appendChild(document.createElement("br"));
        }
    }

    // Step 4: Submit column numbers
    function submitColumns() {
        const inputs = document.querySelectorAll("#column-inputs input");
        columnNumbers = Array.from(inputs).map(input => parseInt(input.value));

        // Transpose the selected columns into rows
        transposedRows = columnNumbers.map(col => {
            return matrix.map(row => row[col - 1] || "").join("");
        });

        document.getElementById("column-input-container").classList.add("hidden");
        document.getElementById("transposed-matrix-container").classList.remove("hidden");
        document.getElementById("transposed-matrix-container").classList.add("fade-in");
        displayMatrix(transposedRows, "transposed-matrix");

        // Create input fields for final column numbers
        const finalColumnInputs = document.getElementById("final-column-inputs");
        finalColumnInputs.innerHTML = "";
        for (let i = 0; i < numLetters; i++) {
            const label = document.createElement("label");
            label.textContent = `Column for letter ${i + 1}: `;
            const input = document.createElement("input");
            input.type = "number";
            input.min = 1;
            input.max = transposedRows[i].length;
            input.required = true;
            finalColumnInputs.appendChild(label);
            finalColumnInputs.appendChild(input);
            finalColumnInputs.appendChild(document.createElement("br"));
        }
    }

    // Step 5: Submit final column numbers and reveal the name
    function submitFinalColumns() {
        const inputs = document.querySelectorAll("#final-column-inputs input");
        const finalColumns = Array.from(inputs).map(input => parseInt(input.value));

        finalMappings = [];
        finalName = "";
        for (let i = 0; i < numLetters; i++) {
            const letter = transposedRows[i][finalColumns[i] - 1];
            finalMappings.push(`${columnNumbers[i]}${finalColumns[i]}-${letter.toUpperCase()}`);
            finalName += letter;
        }

        document.getElementById("transposed-matrix-container").classList.add("hidden");
        document.getElementById("result-container").classList.remove("hidden");
        document.getElementById("result-container").classList.add("fade-in");
        document.getElementById("final-mappings").textContent = finalMappings.join(", ");
        document.getElementById("final-name").textContent = finalName.toUpperCase();
    }

    // Initialize the game
    createMatrix();
</script>
