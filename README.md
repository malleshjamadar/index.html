<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Scientific Calculator</title>
  <style>
    body{margin:0;min-height:100vh;display:grid;place-items:center;font-family:Arial;background:#111;color:#fff}
    .calc{width:min(420px,95vw);background:#1a1f2b;padding:14px;border-radius:12px;border:1px solid #324}
    #display{width:100%;height:64px;font-size:30px;text-align:right;padding:10px;border-radius:8px;border:1px solid #345;background:#0d121b;color:#fff}
    .keys{margin-top:10px;display:grid;grid-template-columns:repeat(5,1fr);gap:8px}
    button{height:44px;border:1px solid #456;border-radius:8px;background:#263144;color:#fff;cursor:pointer}
    .op{background:#2d3e60}.fn{background:#4a2e58}.eq{background:#1f8f4f;font-weight:700}.wide{grid-column:span 2}
  </style>
</head>
<body>
  <div class="calc">
    <input id="display" value="0" readonly />
    <div class="keys">
      <button class="fn" data-v="sin(">sin</button><button class="fn" data-v="cos(">cos</button>
      <button class="fn" data-v="tan(">tan</button><button class="fn" data-v="log(">log</button>
      <button class="fn" data-v="ln(">ln</button>

      <button class="fn" data-v="sqrt(">sqrt</button><button class="fn" data-v="pow10(">10^x</button>
      <button class="fn" data-v="pow2(">x^2</button><button class="fn" data-v="abs(">abs</button>
      <button class="fn" data-v="pi">pi</button>

      <button class="op" data-a="clear">AC</button><button class="op" data-a="back">BS</button>
      <button class="op" data-v="(">(</button><button class="op" data-v=")">)</button>
      <button class="op" data-v="/">/</button>

      <button data-v="7">7</button><button data-v="8">8</button><button data-v="9">9</button>
      <button class="op" data-v="*">*</button><button class="op" data-v="-">-</button>

      <button data-v="4">4</button><button data-v="5">5</button><button data-v="6">6</button>
      <button class="op" data-v="+">+</button><button class="fn" data-v="e">e</button>

      <button data-v="1">1</button><button data-v="2">2</button><button data-v="3">3</button>
      <button data-v=".">.</button><button class="fn" data-v="fact(">n!</button>

      <button class="wide" data-v="0">0</button><button class="op" data-v="%">mod</button>
      <button class="eq wide" data-a="eq">=</button>
    </div>
  </div>

  <script>
    const d = document.getElementById("display");
    const keys = document.querySelector(".keys");
    let exp = "";

    const fact = n => {
      if (!Number.isInteger(n) || n < 0) throw new Error("bad factorial");
      let r = 1; for (let i = 2; i <= n; i++) r *= i; return r;
    };
    const sq = n => n * n;

    const evalSafe = s => {
      const t = s.replaceAll("pi","Math.PI").replaceAll("e","Math.E")
        .replaceAll("sin(","Math.sin(").replaceAll("cos(","Math.cos(").replaceAll("tan(","Math.tan(")
        .replaceAll("log(","Math.log10(").replaceAll("ln(","Math.log(")
        .replaceAll("sqrt(","Math.sqrt(").replaceAll("pow10(","Math.pow(10,")
        .replaceAll("pow2(","sq(").replaceAll("abs(","Math.abs(").replaceAll("fact(","fact(");
      return Function("fact","sq",`return (${t})`)(fact,sq);
    };

    const draw = () => d.value = exp || "0";

    keys.addEventListener("click", e => {
      const b = e.target.closest("button"); if (!b) return;
      if (exp === "Error" && b.dataset.a !== "clear") exp = "";
      if (b.dataset.a === "clear") exp = "";
      else if (b.dataset.a === "back") exp = exp.slice(0,-1);
      else if (b.dataset.a === "eq") {
        try { exp = String(+evalSafe(exp).toFixed(10)); } catch { exp = "Error"; }
      } else exp += b.dataset.v || "";
      draw();
    });

    document.addEventListener("keydown", e => {
      if ((e.key >= "0" && e.key <= "9") || "+-*/%.()".includes(e.key)) { exp += e.key; draw(); }
      else if (e.key === "Enter") { e.preventDefault(); try { exp = String(+evalSafe(exp).toFixed(10)); } catch { exp = "Error"; } draw(); }
      else if (e.key === "Backspace") { exp = exp.slice(0,-1); draw(); }
      else if (e.key === "Escape") { exp = ""; draw(); }
    });
  </script>
</body>
</html>
