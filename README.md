<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Elige tu horario de presentación</title>
    <style>
      *,
      *::before,
      *::after {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
      }
      body {
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
        background: #f5f5f3;
        min-height: 100vh;
        display: flex;
        align-items: flex-start;
        justify-content: center;
        padding: 2rem 1rem;
        color: #1a1a1a;
      }
      .container {
        background: #fff;
        border-radius: 12px;
        border: 1px solid #e5e5e5;
        padding: 2rem;
        width: 100%;
        max-width: 560px;
      }
      h1 {
        font-size: 20px;
        font-weight: 600;
        margin-bottom: 4px;
      }
      .subtitle {
        font-size: 14px;
        color: #666;
        margin-bottom: 1.5rem;
      }
      .section-label {
        font-size: 11px;
        text-transform: uppercase;
        letter-spacing: 0.06em;
        color: #999;
        margin: 1.25rem 0 8px;
      }
      .slot {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 12px 16px;
        border: 1px solid #e5e5e5;
        border-radius: 8px;
        margin-bottom: 8px;
        background: #fff;
      }
      .slot.taken {
        background: #fafafa;
        opacity: 0.6;
      }
      .slot-time {
        font-size: 15px;
        font-weight: 500;
      }
      .slot-date {
        font-size: 13px;
        color: #666;
        margin-top: 2px;
      }
      .slot-owner {
        font-size: 12px;
        color: #999;
        margin-top: 2px;
        font-style: italic;
      }
      .btn-take {
        background: #fff;
        border: 1px solid #d0d0d0;
        border-radius: 6px;
        padding: 6px 14px;
        font-size: 13px;
        cursor: pointer;
        color: #1a1a1a;
      }
      .btn-take:hover {
        background: #f0f0f0;
      }
      .badge-taken {
        font-size: 12px;
        color: #999;
      }
      .success {
        background: #edfdf4;
        border: 1px solid #a3e6c0;
        border-radius: 8px;
        padding: 10px 14px;
        font-size: 14px;
        color: #1a7a45;
        margin-bottom: 1.25rem;
        display: none;
      }
      .error-msg {
        background: #fef2f2;
        border: 1px solid #fca5a5;
        border-radius: 8px;
        padding: 10px 14px;
        font-size: 14px;
        color: #991b1b;
        margin-bottom: 1.25rem;
        display: none;
      }
      .loading {
        font-size: 14px;
        color: #888;
        padding: 8px 0;
      }
      .modal-bg {
        display: none;
        position: fixed;
        inset: 0;
        background: rgba(0, 0, 0, 0.4);
        align-items: center;
        justify-content: center;
        z-index: 100;
        padding: 1rem;
      }
      .modal-bg.open {
        display: flex;
      }
      .modal {
        background: #fff;
        border-radius: 12px;
        padding: 1.5rem;
        width: 100%;
        max-width: 360px;
        border: 1px solid #e5e5e5;
      }
      .modal h2 {
        font-size: 16px;
        font-weight: 600;
        margin-bottom: 4px;
      }
      .modal p {
        font-size: 13px;
        color: #666;
        margin-bottom: 1rem;
      }
      .modal input {
        width: 100%;
        padding: 9px 12px;
        border: 1px solid #d0d0d0;
        border-radius: 6px;
        font-size: 14px;
        font-family: inherit;
        margin-bottom: 12px;
        outline: none;
      }
      .modal input:focus {
        border-color: #888;
      }
      .modal-actions {
        display: flex;
        gap: 8px;
        justify-content: flex-end;
      }
      .btn-cancel {
        background: #fff;
        border: 1px solid #d0d0d0;
        border-radius: 6px;
        padding: 7px 14px;
        font-size: 13px;
        cursor: pointer;
        color: #555;
      }
      .btn-confirm {
        background: #1a1a1a;
        border: none;
        border-radius: 6px;
        padding: 7px 16px;
        font-size: 13px;
        cursor: pointer;
        color: #fff;
        font-weight: 500;
      }
      .btn-confirm:disabled {
        opacity: 0.35;
        cursor: default;
      }
      .btn-confirm:not(:disabled):hover {
        background: #333;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h1>Elige tu horario de presentación</h1>
      <p class="subtitle">
        Selecciona un horario disponible. Cada horario solo puede ser tomado por
        un grupo.
      </p>
      <div class="success" id="successMsg"></div>
      <div class="error-msg" id="errorMsg"></div>
      <div id="slotList"><p class="loading">Cargando horarios...</p></div>
    </div>

    <div class="modal-bg" id="modalBg">
      <div class="modal">
        <h2>Confirmar horario</h2>
        <p id="modalDesc"></p>
        <input
          type="text"
          id="nameInput"
          placeholder="Tu nombre completo"
          autocomplete="off"
        />
        <div class="modal-actions">
          <button class="btn-cancel" onclick="closeModal()">Cancelar</button>
          <button
            class="btn-confirm"
            id="confirmBtn"
            onclick="confirmBooking()"
            disabled
          >
            Confirmar
          </button>
        </div>
      </div>
    </div>

    <script>
      // PEGA AQUÍ LA URL DE TU APPS SCRIPT (la misma que ya tenías)
      const SCRIPT_URL =
        "https://script.google.com/macros/s/AKfycby1Xct7TW3bFVy__eqoxASaHzUTst_1tQTpe-sjY1Ugf4XTdLAii4p7S87YyRI0AkASlw/exec";

      let slots = [];
      let pendingId = null;
      const nameInput = document.getElementById("nameInput");
      const confirmBtn = document.getElementById("confirmBtn");
      nameInput.addEventListener("input", () => {
        confirmBtn.disabled = nameInput.value.trim().length < 2;
      });

      async function loadSlots() {
        try {
          const res = await fetch(`${SCRIPT_URL}?action=list`);
          const data = await res.json();
          slots = data.slots;
          renderSlots();
        } catch (e) {
          document.getElementById("slotList").innerHTML =
            '<p class="loading">Error al cargar los horarios.</p>';
        }
      }

      function renderSlots() {
        const available = slots.filter((s) => !s.alumno);
        const taken = slots.filter((s) => s.alumno);
        let html = "";
        if (available.length === 0) {
          html += '<p class="loading">No quedan horarios disponibles.</p>';
        } else {
          html += '<p class="section-label">Disponibles</p>';
          available.forEach((s) => {
            html += `<div class="slot"><div><div class="slot-time">${s.hora}</div><div class="slot-date">${s.fecha}</div></div><button class="btn-take" onclick="openModal('${s.id}')">Elegir</button></div>`;
          });
        }
        if (taken.length > 0) {
          html += '<p class="section-label">Reservados</p>';
          taken.forEach((s) => {
            html += `<div class="slot taken"><div><div class="slot-time">${s.hora}</div><div class="slot-date">${s.fecha}</div><div class="slot-owner">${s.alumno}</div></div><span class="badge-taken">✓ Reservado</span></div>`;
          });
        }
        document.getElementById("slotList").innerHTML = html;
      }

      function openModal(id) {
        pendingId = id;
        const slot = slots.find((s) => s.id === id);
        document.getElementById(
          "modalDesc"
        ).textContent = `${slot.hora} — ${slot.fecha}`;
        nameInput.value = "";
        confirmBtn.disabled = true;
        document.getElementById("modalBg").classList.add("open");
        setTimeout(() => nameInput.focus(), 50);
      }

      function closeModal() {
        document.getElementById("modalBg").classList.remove("open");
        pendingId = null;
      }

      async function confirmBooking() {
        const name = nameInput.value.trim();
        if (!name || !pendingId) return;
        const bookingId = pendingId;
        confirmBtn.disabled = true;
        confirmBtn.textContent = "Guardando...";
        closeModal();
        try {
          const res = await fetch(
            `${SCRIPT_URL}?action=book&id=${encodeURIComponent(
              bookingId
            )}&alumno=${encodeURIComponent(name)}`
          );
          const data = await res.json();
          if (data.ok) {
            const slot = slots.find((s) => s.id === bookingId);
            slot.alumno = name;
            renderSlots();
            showMsg(
              "successMsg",
              `✓ Horario reservado: ${slot.hora} del ${slot.fecha} para ${name}.`
            );
          } else {
            showMsg(
              "errorMsg",
              "Ese horario ya fue tomado. Por favor elige otro."
            );
            await loadSlots();
          }
        } catch (e) {
          showMsg("errorMsg", "Hubo un error al guardar. Intenta de nuevo.");
        }
        confirmBtn.textContent = "Confirmar";
      }

      function showMsg(id, text) {
        const el = document.getElementById(id);
        el.textContent = text;
        el.style.display = "block";
        setTimeout(() => {
          el.style.display = "none";
        }, 6000);
      }

      document
        .getElementById("modalBg")
        .addEventListener("click", function (e) {
          if (e.target === this) closeModal();
        });
      loadSlots();
    </script>
  </body>
</html>
