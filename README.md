
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Calculator App</title>
  <link rel="stylesheet" href="style.css"/>
</head>
<body>
  <div class="calculator">
    <div class="display" id="display">0</div>
    <div class="buttons">
      <button class="btn" onclick="clearDisplay()">C</button>
      <button class="btn" onclick="appendValue('/')">/</button>
      <button class="btn" onclick="appendValue('*')">*</button>
      <button class="btn" onclick="appendValue('-')">-</button>

      <button class="btn" onclick="appendValue('7')">7</button>
      <button class="btn" onclick="appendValue('8')">8</button>
      <button class="btn" onclick="appendValue('9')">9</button>
      <button class="btn" onclick="appendValue('+')">+</button>

      <button class="btn" onclick="appendValue('4')">4</button>
      <button class="btn" onclick="appendValue('5')">5</button>
      <button class="btn" onclick="appendValue('6')">6</button>
      <button class="btn equal" onclick="calculateResult()">=</button>

      <button class="btn" onclick="appendValue('1')">1</button>
      <button class="btn" onclick="appendValue('2')">2</button>
      <button class="btn" onclick="appendValue('3')">3</button>

      <button class="btn" onclick="appendValue('0')">0</button>
      <button class="btn" onclick="appendValue('.')">.</button>
    </div>
  </div>
  <script src="script.js"></script>
</body>
</html>



body {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: #f2f2f2;
  margin: 0;
  font-family: Arial, sans-serif;
}

.calculator {
  width: 260px;
  background: #222;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 5px 15px rgba(0,0,0,0.3);
}

.display {
  background: #000;
  color: #0f0;
  font-size: 2rem;
  padding: 10px;
  text-align: right;
  border-radius: 5px;
  margin-bottom: 10px;
  height: 50px;
  overflow-x: auto;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

.btn {
  padding: 20px;
  font-size: 1.2rem;
  border: none;
  border-radius: 5px;
  background: #333;
  color: white;
  cursor: pointer;
  transition: background 0.2s;
}

.btn:hover {
  background: #555;
}

.equal {
  grid-column: span 2;
  background: #0a84ff;
}



let display = document.getElementById("display");

function appendValue(value) {
  if (display.innerText === "0" && value !== '.') {
    display.innerText = value;
  } else {
    display.innerText += value;
  }
}

function clearDisplay() {
  display.innerText = "0";
}

function calculateResult() {
  try {
    let result = eval(display.innerText);
    display.innerText = result;
  } catch (error) {
    display.innerText = "Error";
  }
}



