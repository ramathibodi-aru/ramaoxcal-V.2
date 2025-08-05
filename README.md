# ramaoxcal-V.2
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>RAMA OX-Cal generation</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
</head>
<body class="bg-light p-4">
  <div class="container">
    <h2 class="text-center mb-4">🩺 RAMA OX-Cal generation</h2>
    <p class="text-center">โปรแกรมคำนวณการใช้ออกซิเจนสำหรับการเคลื่อนย้ายผู้ป่วย</p>

    <ul class="nav nav-tabs" id="tabMenu" role="tablist">
      <li class="nav-item"><a class="nav-link active" data-bs-toggle="tab" href="#cannula">Cannula / Mask</a></li>
      <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#hfnc">HFNC</a></li>
      <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#ventilator">Ventilator</a></li>
    </ul>

    <div class="tab-content p-4 bg-white border border-top-0">
      <!-- Cannula -->
      <div class="tab-pane fade show active" id="cannula">
        <h5>1. Cannula / Mask</h5>
        <input class="form-control mb-2" id="cFlow" placeholder="Oxygen Flow Rate (L/min)">
        <input class="form-control mb-2" id="cPsi" placeholder="PSI (แรงดันถัง)">
        <select class="form-control mb-2" id="cTank">
          <option value="0.28">🟢 E (0.28)</option>
          <option value="0.16">🔵 D (0.16)</option>
          <option value="1.56">🟠 M (1.56)</option>
          <option value="2.41">🔴 G (2.41)</option>
        </select>
        <input class="form-control mb-2" id="cTravelTime" placeholder="เวลาเดินทาง (นาที)">
        <button class="btn btn-primary" onclick="calcCannula()">คำนวณ</button>
      </div>

      <!-- HFNC -->
      <div class="tab-pane fade" id="hfnc">
        <h5>2. High Flow Nasal Cannula</h5>
        <input class="form-control mb-2" id="hfFlow" placeholder="Total Flow Rate (L/min)">
        <input class="form-control mb-2" id="hfFio2" placeholder="FiO₂ (%)">
        <input class="form-control mb-2" id="hfPsi" placeholder="PSI">
        <select class="form-control mb-2" id="hfTank">
          <option value="0.28">🟢 E (0.28)</option>
          <option value="0.16">🔵 D (0.16)</option>
          <option value="1.56">🟠 M (1.56)</option>
          <option value="2.41">🔴 G (2.41)</option>
        </select>
        <input class="form-control mb-2" id="hfTravelTime" placeholder="เวลาเดินทาง (นาที)">
        <button class="btn btn-primary" onclick="calcHFNC()">คำนวณ</button>
      </div>

      <!-- Ventilator -->
      <div class="tab-pane fade" id="ventilator">
        <h5>3. Ventilator</h5>
        <input class="form-control mb-2" id="vVt" placeholder="Tidal Volume (mL)">
        <input class="form-control mb-2" id="vRR" placeholder="Respiratory Rate (breaths/min)">
        <input class="form-control mb-2" id="vFio2" placeholder="FiO₂ (%)">
        <input class="form-control mb-2" id="vPsi" placeholder="PSI">
        <select class="form-control mb-2" id="vTank">
          <option value="0.28">🟢 E (0.28)</option>
          <option value="0.16">🔵 D (0.16)</option>
          <option value="1.56">🟠 M (1.56)</option>
          <option value="2.41">🔴 G (2.41)</option>
        </select>
        <input class="form-control mb-2" id="vTravelTime" placeholder="เวลาเดินทาง (นาที)">
        <button class="btn btn-primary" onclick="calcVentilator()">คำนวณ</button>
      </div>
    
    </div>
    <footer class="mt-5 text-center small text-muted">
      <hr>
      <p>พัฒนาโดย:</p>
      <p>👨‍⚕️ พว.กฤษฎา ตระกูลแก่นชาเดช<br>👨‍⚕️ พว.นิรัญ สุวรรณโชติ</p>
      <p>โรงพยาบาลรามาธิบดี</p>
    </footer>
  </div>

<script>
function aiComment(time) {
  if (time < 15) return "⚠️ ออกซิเจนอาจไม่เพียงพอ ควรเตรียมถังสำรอง";
  if (time < 30) return "✔️ เพียงพอสำหรับการเคลื่อนย้ายระยะสั้น";
  return "✅ ออกซิเจนเพียงพอสำหรับการเคลื่อนย้ายทั่วไป";
}

function calcCannula() {
  const flow = parseFloat(document.getElementById("cFlow").value);
  const psi = parseFloat(document.getElementById("cPsi").value);
  const tank = parseFloat(document.getElementById("cTank").value);
  const travel = parseFloat(document.getElementById("cTravelTime").value);
  const time = (psi * tank) / flow;
  const safe = time - travel;
  Swal.fire("Cannula / Mask", `ใช้งานได้ ${time.toFixed(1)} นาที<br>${aiComment(safe)}`, "info");
}

function calcHFNC() {
  const flow = parseFloat(document.getElementById("hfFlow").value);
  const fio2 = parseFloat(document.getElementById("hfFio2").value) / 100;
  const psi = parseFloat(document.getElementById("hfPsi").value);
  const tank = parseFloat(document.getElementById("hfTank").value);
  const travel = parseFloat(document.getElementById("hfTravelTime").value);
  const o2flow = flow * ((fio2 - 0.21) / 0.79);
  const time = (psi * tank) / o2flow;
  const safe = time - travel;
  Swal.fire("HFNC", `ใช้ออกซิเจน ${o2flow.toFixed(2)} L/min<br>ใช้งานได้ ${time.toFixed(1)} นาที<br>${aiComment(safe)}`, "info");
}

function calcVentilator() {
  const vt = parseFloat(document.getElementById("vVt").value) / 1000;
  const rr = parseFloat(document.getElementById("vRR").value);
  const fio2 = parseFloat(document.getElementById("vFio2").value) / 100;
  const psi = parseFloat(document.getElementById("vPsi").value);
  const tank = parseFloat(document.getElementById("vTank").value);
  const travel = parseFloat(document.getElementById("vTravelTime").value);
  const mv = vt * rr;
  const o2flow = (mv + 4) * fio2;
  const time = (psi * tank) / o2flow;
  const safe = time - travel;
  Swal.fire("Ventilator", `Minute Volume: ${mv.toFixed(2)}<br>ใช้ออกซิเจน ${o2flow.toFixed(2)} L/min<br>ใช้งานได้ ${time.toFixed(1)} นาที<br>${aiComment(safe)}`, "info");
}
</script>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
