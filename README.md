<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gráfica de Flujo</title>

  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 25px;
            background-color: white;
        }

        .contenedor {
            max-width: 1000px;
            margin: auto;
        }

        .titulo-input {
            display: block;
            width: 60%;
            margin: 15px auto;
            padding: 8px;
            text-align: center;
            font-size: 18px;
            font-weight: bold;
        }

        .etiquetas-ejes {
            display: flex;
            justify-content: center;
            gap: 15px;
            flex-wrap: wrap;
            margin: 15px 0;
        }

        .etiquetas-ejes input {
            padding: 7px;
            font-size: 14px;
            text-align: center;
        }

        .etiquetas-ejes label {
            display: flex;
            align-items: center;
            gap: 5px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
        }

        th, td {
            border: 1px solid #999;
            padding: 7px;
            text-align: center;
        }

        th {
            background-color: #eeeeee;
        }

        th input {
            width: 90%;
            text-align: center;
            font-weight: bold;
            font-size: 15px;
            border: none;
            background: transparent;
        }

        td input {
            width: 85%;
            padding: 5px;
            text-align: center;
            font-size: 14px;
        }

        .grafica-container {
            width: 100%;
            margin-top: 25px;
        }

        #leyendaPersonalizada {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 25px;
            padding: 10px;
            background-color: white;
            font-size: 14px;
        }

        .item-leyenda {
            display: flex;
            align-items: center;
            gap: 7px;
        }

        .simbolo-leyenda {
            width: 25px;
            height: 3px;
            display: inline-block;
        }

        button {
            display: block;
            margin: 15px auto;
            padding: 10px 25px;
            font-size: 15px;
            cursor: pointer;
        }

        #areaCodigoHTML {
            display: none;
            margin-top: 25px;
            padding: 15px;
            border: 1px solid #999;
            background-color: #f5f5f5;
        }

        #codigoImagen {
            width: 100%;
            box-sizing: border-box;
            font-family: Consolas, monospace;
            font-size: 12px;
            resize: vertical;
            padding: 10px;
        }

        .botones-captura {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 10px;
        }

        .botones-captura button {
            margin: 10px 0;
        }

        .instruccion {
            font-size: 14px;
            color: #444;
            text-align: center;
        }
    </style>
</head>

<body>

<div class="contenedor">

    <!-- Título editable de la gráfica -->
    <input
        type="text"
        id="chartTitle"
        class="titulo-input"
        value="Gráfica de Flujo"
        oninput="actualizarGrafica()"
    >

    <!-- Etiquetas editables de los ejes -->
    <div class="etiquetas-ejes">

        <label>
            Eje X:
            <input
                type="text"
                id="nombreX"
                value="X"
                oninput="actualizarGrafica()"
            >
        </label>

        <label>
            Eje Y:
            <input
                type="text"
                id="nombreEjeY"
                value="Flujo (L/Min)"
                oninput="actualizarGrafica()"
            >
        </label>

    </div>


    <!-- Tabla de datos -->
    <table>

        <thead>
            <tr>
                <th>
                    <input type="text" id="nombreXTabla" value="X"
                           oninput="actualizarNombreX()">
                </th>

                <th>
                    <input type="text" id="nombreY" value="Y"
                           oninput="actualizarGrafica()">
                </th>

                <th>
                    <input type="text" id="nombreZ" value="Z"
                           oninput="actualizarGrafica()">
                </th>

                <th>
                    <input type="text" id="nombreW" value="W"
                           oninput="actualizarGrafica()">
                </th>
            </tr>
        </thead>

        <tbody id="tablaDatos"></tbody>

    </table>


    <!-- Área de la gráfica y leyenda -->
    <div class="grafica-container">

        <canvas id="grafica"></canvas>

        <div id="leyendaPersonalizada"></div>

    </div>


    <button onclick="limpiarValores()">
        Limpiar valores
    </button>

    <hr>

    <button onclick="capturarGrafica()">
        Capturar gráfica como HTML
    </button>


    <!-- Área de presentación del código HTML -->
    <div id="areaCodigoHTML">

        <h3>Código HTML de la captura</h3>

        <p class="instruccion">
            Copie el código siguiente para insertar la imagen de la gráfica
            en otro documento HTML.
        </p>

        <textarea
            id="codigoImagen"
            rows="10"
            readonly
            spellcheck="false"
        ></textarea>

        <div class="botones-captura">

            <button onclick="copiarCodigoHTML()">
                Copiar código HTML
            </button>

            <button onclick="cerrarCodigoHTML()">
                Cerrar
            </button>

        </div>

    </div>

</div>


<script>

    // Colores de las tres series
    const colores = {
        Y: "#1976D2",
        Z: "#E53935",
        W: "#2E7D32"
    };


    // Crear las nueve filas: X = 0 hasta 8
    const tabla = document.getElementById("tablaDatos");

    for (let i = 0; i <= 8; i++) {

        const fila = document.createElement("tr");

        fila.innerHTML = `
            <td>
                <input type="number" value="${i}" step="1"
                       id="x${i}" readonly>
            </td>

            <td>
                <input type="number" step="any"
                       id="y${i}" oninput="actualizarGrafica()">
            </td>

            <td>
                <input type="number" step="any"
                       id="z${i}" oninput="actualizarGrafica()">
            </td>

            <td>
                <input type="number" step="any"
                       id="w${i}" oninput="actualizarGrafica()">
            </td>
        `;

        tabla.appendChild(fila);
    }


    // Configuración inicial de la gráfica
    const ctx = document.getElementById("grafica").getContext("2d");

    const grafica = new Chart(ctx, {

        type: "line",

        data: {
            datasets: [

                {
                    label: "Y vs. X",
                    data: [],
                    borderColor: colores.Y,
                    backgroundColor: colores.Y,
                    pointRadius: 5,
                    pointHoverRadius: 7,
                    borderWidth: 2,
                    tension: 0.4,
                    cubicInterpolationMode: "default",
                    spanGaps: false
                },

                {
                    label: "Z vs. X",
                    data: [],
                    borderColor: colores.Z,
                    backgroundColor: colores.Z,
                    pointRadius: 5,
                    pointHoverRadius: 7,
                    borderWidth: 2,
                    tension: 0.4,
                    cubicInterpolationMode: "default",
                    spanGaps: false
                },

                {
                    label: "W vs. X",
                    data: [],
                    borderColor: colores.W,
                    backgroundColor: colores.W,
                    pointRadius: 5,
                    pointHoverRadius: 7,
                    borderWidth: 2,
                    tension: 0.4,
                    cubicInterpolationMode: "default",
                    spanGaps: false
                }

            ]
        },

        options: {

            responsive: true,

            plugins: {

                title: {
                    display: true,
                    text: "Gráfica de Flujo",
                    font: {
                        size: 18
                    }
                },

                legend: {
                    display: false
                }
            },

            scales: {

                x: {
                    type: "linear",
                    min: 0,
                    max: 8,

                    title: {
                        display: true,
                        text: "X"
                    },

                    ticks: {
                        stepSize: 1
                    }
                },

                y: {
                    title: {
                        display: true,
                        text: "Flujo (L/Min)"
                    }
                }

            }
        }
    });


    // Mantener sincronizados los dos campos del eje X
    function actualizarNombreX() {

        const nombre = document.getElementById("nombreXTabla").value;

        document.getElementById("nombreX").value = nombre;

        actualizarGrafica();
    }


    // Actualizar la leyenda personalizada
    function actualizarLeyenda() {

        const leyenda = document.getElementById("leyendaPersonalizada");

        const nombres = [
            document.getElementById("nombreY").value,
            document.getElementById("nombreZ").value,
            document.getElementById("nombreW").value
        ];

        const claves = ["y", "z", "w"];
        const series = ["Y", "Z", "W"];

        leyenda.innerHTML = "";

        series.forEach((serie, indice) => {

            let tieneDatos = false;

            for (let i = 0; i <= 8; i++) {

                const valor = document.getElementById(
                    claves[indice] + i
                ).value;

                if (valor.trim() !== "" &&
                    Number.isFinite(Number(valor))) {

                    tieneDatos = true;
                    break;
                }
            }

            const color = tieneDatos ? colores[serie] : "#FFFFFF";

            const item = document.createElement("div");
            item.className = "item-leyenda";
            item.style.color = color;

            const simbolo = document.createElement("span");
            simbolo.className = "simbolo-leyenda";
            simbolo.style.backgroundColor = color;

            const texto = document.createElement("span");

            texto.textContent =
                nombres[indice] + " vs. " +
                document.getElementById("nombreX").value;

            item.appendChild(simbolo);
            item.appendChild(texto);

            leyenda.appendChild(item);
        });
    }


    // Actualizar datos, nombres y gráfica
    function actualizarGrafica() {

        const nombreX = document.getElementById("nombreX").value;
        const nombreY = document.getElementById("nombreY").value;
        const nombreZ = document.getElementById("nombreZ").value;
        const nombreW = document.getElementById("nombreW").value;

        const etiquetaEjeY =
            document.getElementById("nombreEjeY").value;

        const valoresX = [];
        const valoresY = [];
        const valoresZ = [];
        const valoresW = [];

        for (let i = 0; i <= 8; i++) {

            const x = Number(document.getElementById("x" + i).value);

            const leerValor = (id) => {

                const valor = document.getElementById(id).value;

                if (valor.trim() === "") {
                    return null;
                }

                const numero = Number(valor);

                return Number.isFinite(numero) ? numero : null;
            };

            valoresX.push(x);
            valoresY.push(leerValor("y" + i));
            valoresZ.push(leerValor("z" + i));
            valoresW.push(leerValor("w" + i));
        }


        // Actualizar nombres de las series
        grafica.data.datasets[0].label =
            nombreY + " vs. " + nombreX;

        grafica.data.datasets[1].label =
            nombreZ + " vs. " + nombreX;

        grafica.data.datasets[2].label =
            nombreW + " vs. " + nombreX;


        // Actualizar puntos de cada serie
        grafica.data.datasets[0].data =
            valoresX.map((x, i) => ({
                x: x,
                y: valoresY[i]
            }));

        grafica.data.datasets[1].data =
            valoresX.map((x, i) => ({
                x: x,
                y: valoresZ[i]
            }));

        grafica.data.datasets[2].data =
            valoresX.map((x, i) => ({
                x: x,
                y: valoresW[i]
            }));


        // Actualizar etiquetas de los ejes
        grafica.options.scales.x.title.text = nombreX;

        grafica.options.scales.y.title.text = etiquetaEjeY;


        // Actualizar título de la gráfica
        grafica.options.plugins.title.text =
            document.getElementById("chartTitle").value;


        grafica.update();

        actualizarLeyenda();
    }


    // Limpiar los valores de las columnas Y, Z y W
    function limpiarValores() {

        for (let i = 0; i <= 8; i++) {

            document.getElementById("y" + i).value = "";
            document.getElementById("z" + i).value = "";
            document.getElementById("w" + i).value = "";
        }

        actualizarGrafica();
    }


    // =====================================================
    // CAPTURAR SOLAMENTE LA GRÁFICA Y SU LEYENDA
    // =====================================================

    function capturarGrafica() {

        grafica.update("none");

        const canvasGrafica = document.getElementById("grafica");
        const leyenda = document.getElementById("leyendaPersonalizada");

        const rectCanvas = canvasGrafica.getBoundingClientRect();
        const rectLeyenda = leyenda.getBoundingClientRect();

        const escala = canvasGrafica.width / rectCanvas.width;

        const canvasCaptura = document.createElement("canvas");

        canvasCaptura.width = canvasGrafica.width;

        canvasCaptura.height =
            canvasGrafica.height +
            Math.ceil(rectLeyenda.height * escala);

        const contexto = canvasCaptura.getContext("2d");

        // Fondo blanco
        contexto.fillStyle = "#FFFFFF";

        contexto.fillRect(
            0,
            0,
            canvasCaptura.width,
            canvasCaptura.height
        );

        // Dibujar la gráfica
        contexto.drawImage(
            canvasGrafica,
            0,
            0,
            canvasGrafica.width,
            canvasGrafica.height
        );

        // Dibujar la leyenda
        contexto.save();

        contexto.scale(escala, escala);

        const desplazamientoY = rectCanvas.height;

        const items = leyenda.querySelectorAll(".item-leyenda");

        items.forEach(item => {

            const rectTexto = item.querySelector("span:last-child")
                .getBoundingClientRect();

            const simbolo = item.querySelector(".simbolo-leyenda");
            const rectSimbolo = simbolo.getBoundingClientRect();

            const texto = item.querySelector("span:last-child");
            const estiloTexto = getComputedStyle(texto);

            const xSimbolo = rectSimbolo.left - rectLeyenda.left;
            const ySimbolo = desplazamientoY +
                rectSimbolo.top - rectLeyenda.top;

            contexto.fillStyle =
                getComputedStyle(simbolo).backgroundColor;

            contexto.fillRect(
                xSimbolo,
                ySimbolo,
                rectSimbolo.width,
                rectSimbolo.height
            );

            contexto.fillStyle = getComputedStyle(item).color;
            contexto.font = estiloTexto.font;
            contexto.textBaseline = "middle";

            contexto.fillText(
                texto.textContent,
                rectTexto.left - rectLeyenda.left,
                desplazamientoY +
                rectTexto.top - rectLeyenda.top +
                rectTexto.height / 2
            );

        });

        contexto.restore();


        // Convertir la captura a PNG Base64
        const imagenBase64 = canvasCaptura.toDataURL("image/png");


        // Generar el código HTML de la imagen
        const codigoHTML =
`<div style="width:100%; text-align:center;">
    <img
        src="${imagenBase64}"
        alt="Captura de la gráfica"
        style="display:block; width:100%; max-width:1000px; height:auto; margin:auto;"
    >
</div>`;


        // Presentar el código generado
        document.getElementById("codigoImagen").value = codigoHTML;

        document.getElementById("areaCodigoHTML").style.display = "block";

        document.getElementById("areaCodigoHTML").scrollIntoView({
            behavior: "smooth",
            block: "start"
        });

        document.getElementById("codigoImagen").focus();
        document.getElementById("codigoImagen").select();

    }


    // Copiar el código HTML al portapapeles
    async function copiarCodigoHTML() {

        const campo = document.getElementById("codigoImagen");

        campo.focus();
        campo.select();

        try {

            if (navigator.clipboard && window.isSecureContext) {

                await navigator.clipboard.writeText(campo.value);

            } else {

                const copiado = document.execCommand("copy");

                if (!copiado) {
                    throw new Error("No se pudo copiar automáticamente.");
                }
            }

            alert("El código HTML de la gráfica fue copiado correctamente.");

        } catch (error) {

            alert(
                "No se pudo copiar automáticamente. " +
                "Seleccione el código y utilice Ctrl+C."
            );

        }
    }


    // Cerrar el área del código generado
    function cerrarCodigoHTML() {

        document.getElementById("areaCodigoHTML").style.display = "none";

    }


    // Inicializar la gráfica y la leyenda
    actualizarGrafica();

</script>


</body>
</html>
