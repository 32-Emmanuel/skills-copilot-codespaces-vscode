<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Fictional Product</title>
  <link rel="stylesheet" href="styles.css"/>
</head>
<body>
  <header class="hero">
    <div class="hero-content">
      <h1>Transform Your Workflow</h1>
      <p>Boost productivity with our all-in-one management tool for teams.</p>
      <a href="#" class="cta-button">Get Started</a>
    </div>
  </header>

  <section class="features">
    <div class="feature">
      <img src="/Picture/Photo of Emmanuel Nsa/64" alt="Feature 1 icon" />
      <h3>Collaboration</h3>
      <p>Work together in real time, no matter where your team is.</p>
    </div>
    <div class="feature">
      <img src="/Picture/Photo of Emmanuel Nsa/64" alt="Feature 2 icon" />
      <h3>Automation</h3>
      <p>Automate repetitive tasks to focus on what matters most.</p>
    </div>
    <div class="feature">
      <img src="/Picture/Photo of Emmanuel Nsa/64" alt="Feature 3 icon" />
      <h3>Analytics</h3>
      <p>Track progress with detailed reports and performance insights.</p>
    </div>
  </section>

  <footer class="footer">
    <div>
      <p>Contact us: lalaskiensa@gmail.com | +2347032502975</p>
      <p>&copy; 2025 Fictional Product. All rights reserved.</p>
    </div>
  </footer>
</body>
</html>

{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', sans-serif;
    line-height: 1.6;
    color: #333;
}

.hero {
    background-color: #4f46e5;
    color: #fff;
    padding: 4rem 2rem;
    text-align: center;
}

.hero h1 {
    font-size: 2.5rem;
    margin-bottom: 1rem;
}

.hero p {
    font-size: 1.2rem;
    margin-bottom: 2rem;
}

.cta-button {
    background-color: #fff;
    color: #4f46e5;
    padding: 0.75rem 1.5rem;
    text-decoration: none;
    font-weight: bold;
    border-radius: 5px;
}

.features {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    padding: 4rem 2rem;
    background-color: #f9f9f9;
}

.feature {
    flex: 1 1 300px;
    max-width: 300px;
    margin: 1rem;
    text-align: center;
}

.feature img {
    width: 64px;
    height: 64px;
    margin-bottom: 1rem;
}

.footer {
    background-color: #1f2937;
    color: #fff;
    text-align: center;
    padding: 2rem 1rem;
    font-size: 0.9rem;
}


