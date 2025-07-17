# Malla-interactiva-odonto-unab-

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Malla Interactiva Odonto UNAB</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #fff0f5;
            margin: 0;
            padding: 20px;
            overflow-x: auto;
        }
        h1 {
            text-align: center;
            color: #d96c9b;
        }
        .semestre {
            display: inline-block;
            vertical-align: top;
            width: 200px;
            margin: 10px;
            padding: 10px;
            background: #ffeaf1;
            border-radius: 10px;
        }
        .semestre h2 {
            text-align: center;
            color: #b34782;
        }
        .ramo {
            background: #ccc;
            color: #888;
            padding: 8px;
            margin: 6px 0;
            border-radius: 6px;
            text-align: center;
            cursor: not-allowed;
            transition: 0.3s;
        }
        .ramo.activo {
            background: #f8bcd0;
            color: #4b1c2e;
            cursor: pointer;
        }
        .ramo.tachado {
            text-decoration: line-through;
            opacity: 0.6;
        }
        .ramo.anual {
            border: 2px dashed #a64d79;
        }
        #reset {
            margin: 20px auto;
            display: block;
            padding: 10px 20px;
            background-color: #f06292;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            cursor: pointer;
        }
        .contenedor {
            white-space: nowrap;
        }
    </style>
</head>
<body>
    <h1>Malla Interactiva Odontología UNAB</h1>
    <button id="reset">Reiniciar Malla</button>
    <div class="contenedor" id="malla">
        <!-- Los semestres y ramos serán insertados por JavaScript -->
    </div>
    <script>
        const ramos = {
            "Anatomía Humana": { sem: 1, req: [] },
            "Biología Celular": { sem: 1, req: [] },
            "Física Aplicada": { sem: 1, req: [] },
            "Introducción a la Odontología": { sem: 1, req: [] },
            "Habilidades Comunicativas": { sem: 1, req: [] },

            "Anatomía Aplicada": { sem: 2, req: ["Anatomía Humana", "Biología Celular"] },
            "Química General": { sem: 2, req: [] },
            "Genética Molecular": { sem: 2, req: ["Biología Celular"] },
            "Histología General": { sem: 2, req: ["Anatomía Humana", "Biología Celular"] },
            "Introducción Clínica": { sem: 2, req: ["Introducción a la Odontología"] },
            "Inglés I": { sem: 2, req: [] },

            "Bioquímica General": { sem: 3, req: ["Química General", "Biología Celular"] },
            "Microbiología General": { sem: 3, req: ["Genética Molecular"] },
            "Patología General I": { sem: 3, req: ["Genética Molecular", "Histología General", "Anatomía Aplicada"] },
            "Histología Oral": { sem: 3, req: ["Anatomía Aplicada", "Histología General"] },
            "Fisiología": { sem: 3, req: ["Anatomía Aplicada", "Histología General"] },
            "Lab Fisiología": { sem: 3, req: ["Anatomía Aplicada", "Histología General"] },
            "Inglés II": { sem: 3, req: ["Inglés I"] },

            "Biomateriales Dentales": { sem: 3.5, req: ["Física Aplicada", "Anatomía Aplicada", "Química General"], anual: true }
        };

        const mallaDiv = document.getElementById("malla");

        for (let s = 1; s <= 12; s++) {
            const cont = document.createElement("div");
            cont.className = "semestre";
            cont.innerHTML = `<h2>${s}° Sem</h2>`;
            for (let [nombre, datos] of Object.entries(ramos)) {
                const visibleEn = datos.anual ? [Math.floor(datos.sem), Math.ceil(datos.sem)] : [datos.sem];
                if (visibleEn.includes(s)) {
                    const div = document.createElement("div");
                    div.className = "ramo" + (datos.anual ? " anual" : "");
                    div.textContent = nombre;
                    div.dataset.nombre = nombre;
                    if (datos.req.length === 0) div.classList.add("activo");
                    cont.appendChild(div);
                }
            }
            mallaDiv.appendChild(cont);
        }

        function actualizarEstado() {
            document.querySelectorAll(".ramo").forEach(div => {
                const nombre = div.dataset.nombre;
                const datos = ramos[nombre];
                if (div.classList.contains("tachado")) return;
                const cumplidos = datos.req.every(req => {
                    return document.querySelectorAll(`.ramo[data-nombre='${req}']`).length &&
                           [...document.querySelectorAll(`.ramo[data-nombre='${req}']`)]
                           .every(d => d.classList.contains("tachado"));
                });
                if (cumplidos) div.classList.add("activo");
            });
        }

        document.addEventListener("click", (e) => {
            if (e.target.classList.contains("activo")) {
                e.target.classList.add("tachado");
                e.target.classList.remove("activo");
                actualizarEstado();
            }
        });

        document.getElementById("reset").addEventListener("click", () => {
            document.querySelectorAll(".ramo").forEach(div => {
                div.classList.remove("tachado", "activo");
                if (ramos[div.dataset.nombre].req.length === 0)
                    div.classList.add("activo");
            });
        });

        actualizarEstado();
    </script>
</body>
</html>
