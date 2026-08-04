# **🎹 Memoria Técnica y Documentación del Proyecto: Mark Chords**

**Proyecto:** Mark Chords \- Visor y Transpositor de Cifrados en Vivo

**Iglesia:** Agua Fresca

**Pianista / Usuario Principal:** Mark

**Versión:** 2.5 (Edición Standalone Web Application)

**Fecha de Documentación:** Agosto 2026

## **📌 1\. Descripción General**

**Mark Chords** es una aplicación web *single-page* (de una sola página) diseñada específicamente para la ministración de música, práctica y ejecución en vivo de piano/teclado en la **Iglesia Agua Fresca**.

La herramienta soluciona los problemas comunes del pianista en vivo:

1. **Transposición instantánea:** Cambiar de tono cualquier cifrado en tiempo real sin deformar la letra.  
2. **Cifrados limpios y legibles:** Formateo automático de acordes (destacados visualmente) e indicadores de sección (\[INTRO\], \[CORO\], \[PUENTE\]).  
3. **Modo En Vivo (Stage Mode):** Interfaz oscura de alto contraste, tamaño de letra ampliado y **Auto-Scroll** manos libres con velocidad ajustable.  
4. **Sincronización de Repertorio:** Capacidad de cargar archivos de Excel (.xlsx) y sincronizar automáticamente con un **Google Sheet** público para mantener el repertorio de la iglesia actualizado.  
5. **Herramientas de Ensayo:** Reproductor de secuencias MP3 integrado (con control de velocidad 0.75x, 1.0x, 1.25x) e integración con videos de referencia en **YouTube**.  
6. **Anotaciones del Teclado:** Espacio dedicado para parches de sintetizador, indicaciones de bajo invertido (ej. A/C\#), entradas y dinámicas.

## **🛠️ 2\. Arquitectura y Stack Tecnológico**

El proyecto está diseñado bajo una arquitectura **Single-File App** (todo contenido en un único archivo index.html), lo que facilita su ejecución local abriendo el archivo en cualquier navegador o su despliegue inmediato en hosting estático.

### **Tecnologías y Librerías (vía CDN):**

* **HTML5 & JavaScript Vanilla (ES6+):** Lógica principal, manipulaciones del DOM y algoritmo de transposición cromática.  
* **Tailwind CSS:** Framework CSS por CDN para interfaz responsiva y estética oscura moderna (bg-slate-950, bg-slate-900).  
* **FontAwesome 6.4.0:** Iconografía musical y de controles de navegación.  
* **SheetJS (xlsx.full.min.js v0.18.5):** Parseo y lectura de archivos Excel (.xlsx, .xls, .csv) y conversión a JSON en el navegador.  
* **Google Fonts:**  
  * Inter: Tipografía de interfaz y controles.  
  * JetBrains Mono: Tipografía monoespaciada para alineación perfecta de acordes y letras.

## **📁 3\. Estructura del Archivo del Proyecto**

mark-chords/  
├── index.html            \# Aplicación completa (HTML \+ CSS Tailwind \+ JavaScript)  
└── PROJECT\_MEMORY.md     \# Este archivo de memoria y documentación

## **📊 4\. Estructura de Datos (Modelo de Canción)**

Las canciones se almacenan en el localStorage del navegador bajo la clave mark\_chords\_songs. Cada canción responde al siguiente esquema JSON:

{  
  "id": "rey-christine",  
  "title": "Rey",  
  "artist": "Christine D'Clario",  
  "key": "D",  
  "bpm": 72,  
  "youtube": "https://www.youtube.com/watch?v=-5P\_f-ed13s",  
  "mp3": "https://enlace-al-audio-secuencia.mp3",  
  "sequence": \["INTRO", "VERSO 1", "VERSO 2", "CORO", "INTERLUDIO", "VERSO 1", "VERSO 2", "CORO", "PUENTE", "FINAL"\],  
  "annotations": "Agua Fresca \- Pad de fondo \+ Piano acentuado. Bajo invertido en A/C\#.",  
  "chords": "\[INTRO\]\\nD  A/C\#  Bm  G\\n\\n\[VERSO 1\]\\nD\\n En tu presencia danzamos libres..."  
}

### **Formato Recomendado para la Columna/Mapeo de Excel y Google Sheets:**

El parser busca automáticamente las siguientes cabeceras en archivos .xlsx o tablas sincronizadas:

* **Título / Song / Nombre / Cancion:** Nombre del tema.  
* **Artista / Ministerio / Artist:** Ejecutante original.  
* **Tono / Key / Tonalidad:** Tono original (ej: D, G, E, F\#).  
* **BPM / Tempo:** Pulsaciones por minuto.  
* **YouTube / Link / Video:** URL de YouTube.  
* **MP3 / Audio / Secuencia:** Enlace directo al MP3 de ensayo.  
* **Estructura / Secuencia Texto / Orden:** Partes separadas por coma (ej. INTRO, VERSO, CORO, PUENTE).  
* **Anotaciones / Notas / Teclado:** Comentarios del pianista.  
* **Cifrado / Acordes / Chords:** Texto completo con acordes y letras.

## **⚙️ 5\. Algoritmos Clave e Implementación Interna**

1. **Transposición Cromática (transposeNote / transposeChord):**  
   * Mapea las 12 notas de la escala cromática usando dos arrays (CHROMATIC\_SCALE\_SHARP y CHROMATIC\_SCALE\_FLAT).  
   * Detecta si la tonalidad resultante utiliza bemoles (F, Bb, Eb, Ab, Db) o sostenidos para aplicar la nomenclatura adecuada.  
   * Aplica Expresiones Regulares (/(\[A-G\]\[b\#\]?...)/gi) para identificar los acordes en el texto y transportarlos sin alterar las letras de la canción.  
2. **Reconocimiento de Líneas de Acordes (isChordLine):**  
   * Analiza cada línea del cifrado. Si todos los tokens coinciden con patrones armónicos (Bm, A/C\#, G2, Dadd9, Fsus4), aplica un badge visual especial con clase .chord-badge.  
   * Las líneas entre corchetes (ej. \[CORO\]) se convierten automáticamente en encabezados estilizados .section-header.  
3. **Auto-Scroll para Escenario (toggleAutoScroll):**  
   * Ejecuta un bucle continuo sobre el contenedor \#stageScrollArea.  
   * Permite regular la velocidad de desplazamiento en tiempo real mediante un slider de 1x a 10x.  
4. **Sincronización con Google Sheets (syncFromGoogleSheet):**  
   * Extrae el ID de la hoja (spreadsheetId) y el identificador de pestaña (gid) de la URL de Google Sheets enviada por el usuario.  
   * Realiza una petición fetch() al endpoint de exportación CSV público:  
     https://docs.google.com/spreadsheets/d/{spreadsheetId}/gviz/tq?tqx=out:csv\&gid={gid}  
   * Incluye una alternativa de pegado manual directo en caso de restricciones de CORS o red local.

## **🚀 6\. Guía de Migración y Despliegue**

### **Opción A: Ejecución Local**

1. Descarga o copia el archivo index.html.  
2. Haz doble clic sobre index.html para abrirlo en Chrome, Edge, Safari o Firefox.  
3. No requiere servidor Node.js ni compilación previo.

### **Opción B: Despliegue Gratis en la Nube (GitHub Pages / Netlify / Vercel)**

1. Crea un repositorio en GitHub (ejemplo: mark-chords).  
2. Sube el archivo index.html en la raíz del repositorio.  
3. Activa **GitHub Pages** desde la pestaña *Settings \> Pages* seleccionando la rama main / root.  
4. Obtendrás un enlace público accesible desde iPad, tablet o teléfono en el escenario (ejemplo: https://tu-usuario.github.io/mark-chords).

### **Opción C: Carga del Repertorio Real desde Google Sheets**

1. Crea una hoja de cálculo en Google Sheets con las columnas: Título, Artista, Tono, BPM, YouTube, MP3, Estructura, Anotaciones, Cifrado.  
2. Ve a *Archivo \> Compartir \> Compartir con otros* y cambia el acceso a **"Cualquier persona con el enlace puede ver"**.  
3. En la app Mark Chords, haz clic en **"Sincronizar Google Sheet"**, pega el enlace y presiona **"Sincronizar Ahora"**.

## **📝 7\. Enlace del Repertorio Oficial**

* **Google Sheet de Referencia:**  
  https://docs.google.com/spreadsheets/d/1H\_dIP8OhvhnYxvtHTEOjgFq3UvubcG89vkIJeE9VtHc/edit?gid=0\#gid=0

## **🔮 8\. Posibles Mejoras Futuras**

* Agregar metrónomo integrado (sonoro y visual) basado en el BPM de cada canción.  
* Soporte para pedales Bluetooth de cambio de página / scroll (teclas PageDown / ArrowDown).  
* Exportación del cifrado en formato PDF para impresión rápida.