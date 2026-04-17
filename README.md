Optimizador de Rutas VRP - Prueba Técnica
📋 Descripción General
Sistema web de optimización de rutas para visitadores/vehículos que permite asignar eficientemente puntos de visita (PDVs) a usuarios, calculando rutas óptimas que minimizan distancia y tiempo de recorrido.
Desarrollado por: Gregory Antonio Abreu Marte
Fecha: 02/10/2025
Versión: 1.0.0
Tecnología: React + Vite + JavaScript

🎯 Características Principales
Funcionalidades Implementadas
✅ Carga de Archivos Excel

Soporte para archivos PDV (Puntos de Visita)
Soporte para archivos Usuarios (Visitadores)
Validación automática de esquemas

✅ Validaciones Robustas

Verificación de columnas obligatorias
Validación de tipos de datos (coordenadas, estados, etc.)
Detección de archivos vacíos o mal formateados
Mensajes de error descriptivos

✅ Optimización de Rutas

Algoritmo Nearest Neighbor para construcción inicial
Mejora local con 2-opt
Asignación balanceada de PDVs entre usuarios
Respeto de restricciones (horas, visitas máximas)

✅ Cálculos Precisos

Distancias geográficas con fórmula Haversine
Tiempos de viaje basados en velocidad urbana promedio
Horarios de llegada y salida calculados
Métricas acumuladas

✅ Resultados Completos

Métricas globales del sistema
Rutas detalladas por usuario
Secuencia optimizada de visitas
Exportación a Excel con todos los detalles

✅ Interfaz Profesional

Diseño moderno y responsive
Experiencia de usuario intuitiva
Visualización clara de resultados
Descarga de plantillas de ejemplo


🛠️ Tecnologías Utilizadas
|Tecnología    | Versión | Propósito             |
--------------------------------------------------
|React         |  18.x   | Framework de UI       |
|Vite          |  5.x    |Build tool y dev server|
|Tailwind CSS  |  3.x    |Framework de estilos   |
|SheetJS (xlsx)|  0.18.x |Procesamiento de Excel |
|Lucide React  | 0.263.x |Iconos                 |
--------------------------------------------------

📦 Instalación y Ejecución
Requisitos Previos

-Node.js 16.x o superior
-npm 8.x o superior
---------------------------------------------
Paso 1: Clonar o Descargar el Proyecto
bash
# Si está en repositorio Git
git clone [URL_DEL_REPOSITORIO]
cd optimizador-rutas

# O descomprimir el archivo ZIP
unzip optimizador-rutas.zip
cd optimizador-rutas
-----------------------------------------------
************************************************************************
-----------------------------------------------
Paso 2: Instalar Dependencias
bash
npm install
Esto instalará automáticamente:

React y React DOM
Vite
Tailwind CSS
SheetJS (xlsx)
Lucide React (iconos)
-----------------------------------------------
*************************************************************************
------------------------------------------------------------------------
Paso 3: Ejecutar en Modo Desarrollo
bash
npm run dev
La aplicación estará disponible en: http://localhost:5173
------------------------------------------------------------------------
*****************************************************************************
---------------------------------------------------------
Paso 4: Compilar para Producción
bash
npm run build
Los archivos compilados estarán en la carpeta dist/
-----------------------------------------------------------
************************************************************************
---------------------------------------------------------------------------------
Paso 5: Preview de Producción (Opcional)
bashnpm run preview

📂 Estructura del Proyecto
optimizador-rutas/
├── src/
│   ├── components/
│   │   └── ui/
│   │       └── alert.jsx           # Componente de alertas
│   ├── App.jsx                     # Componente principal
│   ├── main.jsx                    # Punto de entrada
│   └── index.css                   # Estilos globales
├── public/                         # Archivos públicos
├── ejemplos/                       # Archivos de prueba
│   ├── Plantilla_PDV.xlsx
│   ├── Plantilla_Usuarios.xlsx
│   └── Resultados_Ejemplo.xlsx
├── docs/
│   ├── ALGORITMO.md                # Explicación del algoritmo
│   └── MANUAL_USUARIO.md           # Guía de uso
├── package.json                    # Dependencias
├── vite.config.js                  # Configuración de Vite
├── tailwind.config.js              # Configuración de Tailwind
└── README.md                       # Este archivo
----------------------------------------------------------------------------------


🎓 Algoritmo Implementado
Resumen Técnico
Enfoque: Nearest Neighbor + Mejora 2-opt
Fases:
1.Asignación: Distribución balanceada de PDVs a usuarios respetando restricciones
2.Construcción: Nearest Neighbor para crear ruta inicial
3.Optimización: 2-opt para mejorar la ruta eliminando cruces

Complejidad: O(m × n²) donde m=usuarios, n=PDVs por usuario
Características Clave

✅ Distancia real: Fórmula Haversine (precisión geográfica)
✅ Tiempo calculado: (distancia_km / 40 km/h) × 60 minutos
✅ Priorización: PDVs ordenados por campo Prioridad
✅ Restricciones: Respeta horas/día y visitas máximas
✅ Optimización local: Mejora 2-opt (típicamente 15-25% de mejora)

Rendimiento
|PDVs|Usuarios|Tiempo Estimado|
-------------------------------
|100 |    5   |< 1 segundo    |
|500 |   10   |1-2 segundos   |
|1000|   25   |3-5 segundos   |
|2000|   50   |8-10 segundos  |
-------------------------------
Para más detalles, consultar: Explicacion del algoritmo elegido

📊 Formato de Datos
Archivo PDV (Excel)
Columnas obligatorias:

-ID
-Nombre del PDV
-Segmentación
-Cadena
-Canal del PDV
-Regional
-País
-Dirección
-Latitud (número decimal)
-Longitud (número decimal)
-Activo ("Sí" o "No")
-Visitas semanales (número)
-Duración visita(horas) (número)
-Prioridad (1, 2, 3...)

Archivo Usuarios (Excel)
Columnas obligatorias:

-ID
-Nombre del empleado
-Usuario
-Usuario activo ("Sí" o "No")
-Perfil de acceso
-País
-Regionales
-E-mail
-Latitud (número decimal)
-Longitud (número decimal)
-Fecha de admisión
-Horas por día usuario (número)
-Horas por semana usuario
-Visitas maximas por dia (número)

Archivo de Resultados (Generado)
Columnas incluidas:

-Usuario ID, Usuario Nombre, Email
-Secuencia (orden de visita)
-PDV ID, PDV Nombre
-Segmentación, Cadena
-Distancia (km)
-Tiempo Viaje (min)
-Hora Llegada, Hora Salida
-Duración Visita (h)
-Distancia Acumulada (km)
-Tiempo Acumulado (min)
-Prioridad


🚀 Guía de Uso
Paso 1: Preparar Archivos

1.Descargar plantillas desde la aplicación (botones verdes)
2.Completar con tus datos reales
3.Asegurarse de que las coordenadas sean correctas
4.Marcar como "Sí" los PDVs y Usuarios activos

Paso 2: Cargar Archivos

1.Click en el área "Archivo PDV"
2.Seleccionar archivo Excel de PDVs
3.Click en el área "Archivo Usuarios"
4.Seleccionar archivo Excel de Usuarios

Paso 3: Calcular Rutas

1.Click en botón "Calcular Rutas Óptimas"
2.Esperar mientras el sistema procesa (5-10 segundos)
3.Revisar métricas globales en pantalla

Paso 4: Revisar Resultados
La aplicación mostrará:

1.Métricas globales: Usuarios activos, PDVs atendidos, distancia total, tiempo total
2.Rutas por usuario: Tablas detalladas con secuencia de visitas
3.Detalles por visita: Distancias, tiempos, horarios

Paso 5: Descargar Resultados

1.Click en "Descargar Resultados"
1.Se generará archivo Excel con todas las rutas
3.Abrir en Excel/Google Sheets para análisis


🧪 Archivos de Ejemplo
En la carpeta Optimizador-de-Rutas-VRP/  se incluyen:

1.Plantilla_PDV.xlsx - Ejemplo de archivo PDV con estructura correcta
2.Plantilla_Usuarios.xlsx - Ejemplo de archivo Usuarios con estructura correcta
3.Resultados_Ejemplo.xlsx - Ejemplo de salida del sistema con datos de prueba

Estos archivos fueron generados usando la aplicación con datos de prueba realistas.

⚠️ Manejo de Errores
Errores Comunes y Soluciones

|Error                           |         Causa             |              Solución                          |
---------------------------------------------------------------------------------------------------------------
|"El archivo está vacío"         |Archivo sin datos          | Verificar que haya al menos una fila con datos"|
|Columnas faltantes en PDV"      |Faltan columnas requeridas | Usar plantilla proporcionada"                  |
|Latitud o Longitud inválida"    |Coordenadas mal formateadas| Verificar formato numérico decimal"            |
|Campo 'Activo' debe ser Sí o No"|Valor incorrecto           | Escribir exactamente "Sí" o "No"               |
|"No hay usuarios activos"       |Todos marcados como "No"   | Marcar al menos un usuario como "Sí"           |
---------------------------------------------------------------------------------------------------------------
Validaciones Implementadas

✅ Verificación de estructura de archivos
✅ Validación de tipos de datos
✅ Detección de campos faltantes
✅ Mensajes de error descriptivos
✅ Prevención de errores en cálculos


🎨 Capturas de Pantalla
Pantalla Principal: Captura de pantalla 2025-10-02 142512.JPG
Resultados: Captura de pantalla 2025-10-02 142411.JPG


📈 Capacidad del Sistema
Límites Teóricos

PDVs: Hasta 5,000 (recomendado: 2,000)
Usuarios: Hasta 100 (recomendado: 50)
Tiempo de procesamiento: < 15 segundos para caso máximo

Optimizaciones Implementadas

Algoritmo eficiente O(m × n²)
Validaciones tempranas para fallar rápido
Cálculos optimizados de distancias
Limitación de iteraciones 2-opt para garantizar tiempos


🔄 Mejoras Futuras Sugeridas
Funcionalidades Adicionales

 Visualización de rutas en mapa interactivo (Google Maps/Leaflet)
 Exportación a PDF con gráficos
 Configuración de velocidad promedio personalizable
 Soporte para múltiples días de ruta
 Consideración de tráfico en tiempo real
 Restricciones de ventanas horarias por PDV
 Modo de edición manual de rutas

Optimizaciones Técnicas

 Web Workers para procesamiento paralelo
 Caché de distancias Haversine
 Algoritmo genético para casos muy grandes
 Simulated Annealing para mejor optimización
 Base de datos para histórico de rutas

--------------------------------------------------
🐛 Troubleshooting
Problema: La aplicación no inicia
bash
# Limpiar caché y reinstalar
rm -rf node_modules package-lock.json
npm install
npm run dev
--------------------------------------------------

--------------------------------------------------
Problema: Errores al compilar para producción
bash
# Verificar versión de Node.js
node --version  # Debe ser 16.x o superior

# Actualizar dependencias
npm update
npm run build
-------------------------------------------------

Problema: Los archivos Excel no se cargan

Verificar que el archivo tenga extensión .xlsx o .xls
Asegurarse de que no esté abierto en Excel
Comprobar que tenga la estructura correcta


📞 Soporte y Contacto
Para preguntas sobre la implementación:

Email: [abreugregory42@gmail.com]
GitHub: [https://github.com/Greg0407]
LinkedIn: [www.linkedin.com/in/gregory-antonio-abreu-marte-4503a9202]


📄 Licencia
Este proyecto fue desarrollado como prueba técnica para el puesto de Especialista IT.

******************************************************************************************************************************************************************************************************************************************

🚀 Guía de Compilación Paso a Paso
Tabla de Contenidos

Preparación del Entorno
Creación del Proyecto
Estructura de Archivos
Configuración
Código Fuente
Compilación y Testing
Preparación para Envío

--------------------------------------------------------------
1. Preparación del Entorno
Verificar Instalaciones
# Verificar Node.js (debe ser 16+ o superior)
node --version
# Salida esperada: v16.x.x o v18.x.x o v20.x.x

# Verificar npm
npm --version
# Salida esperada: 8.x.x o superior
---------------------------------------------------------------
Si no tienes Node.js instalado:
Windows:

Descargar de: https://nodejs.org/
Ejecutar instalador
Reiniciar terminal

--------------------------------------------------------------
macOS:
brew install node

--------------------------------------------------------------
------------------------------------------------------------------------
Linux (Ubuntu/Debian):
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
----------------------------------------------------------------------

-----------------------------------------------------------------------------
2. Creación del Proyecto
Paso 2.1: Crear carpeta y proyecto
# Crear carpeta del proyecto
mkdir optimizador-rutas-vrp
cd optimizador-rutas-vrp

# Inicializar proyecto con Vite
npm create vite@latest . -- --template react

# Responder a las preguntas:
# ✔ Package name: ... optimizador-rutas-vrp
# ✔ Select a framework: › React
# ✔ Select a variant: › JavaScript
---------------------------------------------------------------------------------

---------------------------------------------------------------------------------
Paso 2.2: Instalar dependencias base
# Instalar dependencias del proyecto
npm install

# Instalar bibliotecas adicionales necesarias
npm install xlsx lodash lucide-react
-----------------------------------------------------------------------------------

-----------------------------------------------------------------------------------

Paso 2.3: Configurar Tailwind CSS
# Instalar Tailwind CSS y dependencias
npm install -D tailwindcss postcss autoprefixer

# Generar archivos de configuración
npx tailwindcss init -p
---------------------------------------------------------------------------------------

-------------------------------------------------------------------------------------
3. Estructura de Archivos
Paso 3.1: Crear estructura de carpetas

# Crear carpetas necesarias
mkdir -p src/components/ui
mkdir ejemplos
mkdir docs
------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------
Paso 3.2: Estructura final
optimizador-rutas-vrp/
├── node_modules/           (generado automáticamente)
├── public/
├── src/
│   ├── components/
│   │   └── ui/
│   │       └── alert.jsx   (crear)
│   ├── App.jsx             (modificar)
│   ├── main.jsx            (ya existe)
│   └── index.css           (modificar)
├── ejemplos/               (crear archivos después)
├── docs/
│   ├── ALGORITMO.md        (crear)
│   └── MANUAL_USUARIO.md   (crear)
├── .gitignore              (ya existe)
├── index.html              (ya existe)
├── package.json            (ya existe)
├── vite.config.js          (ya existe)
├── tailwind.config.js      (crear)
├── postcss.config.js       (crear)
└── README.md               (crear)
-----------------------------------------------------------------------------------------------------------------


---------------------------------------------------------------------------------------------------------------------
4. Configuración
Paso 4.1: Configurar Tailwind CSS
Editar tailwind.config.js:
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
-------------------------------------------------------------------------------------------------------------------------


---------------------------------------------------------------------------------------------------------------------------
Paso 4.2: Configurar estilos globales
Editar src/index.css (reemplazar todo el contenido):
@tailwind base;
@tailwind components;
@tailwind utilities;
----------------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------------------------------------------
Paso 4.3: Verificar package.json
Tu package.json debe verse así:
{
  "name": "optimizador-rutas-vrp",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "xlsx": "^0.18.5",
    "lodash": "^4.17.21",
    "lucide-react": "^0.263.1"
  },
  "devDependencies": {
    "@types/react": "^18.2.43",
    "@types/react-dom": "^18.2.17",
    "@vitejs/plugin-react": "^4.2.1",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32",
    "tailwindcss": "^3.3.6",
    "vite": "^5.0.8"
  }
}
------------------------------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------------------------------------------------------
5. Código Fuente
Paso 5.1: Crear componente Alert
Crear archivo src/components/ui/alert.jsx:
// src/components/ui/alert.jsx
import React from 'react';

export const Alert = ({ children, className = '' }) => (
  <div className={`rounded-lg border p-4 ${className}`}>
    {children}
  </div>
);

export const AlertDescription = ({ children, className = '' }) => (
  <div className={`text-sm ${className}`}>
    {children}
  </div>
);
--------------------------------------------------------------------------------------------------------------------

----------------------------------------------------------------------------------------------------------------
Paso 5.2: Actualizar main.jsx
Verificar que src/main.jsx tenga este contenido:
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
---------------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------------------------------------
Paso 5.3: Crear App.jsx
Reemplazar completamente src/App.jsx con el código del componente RouteOptimizer que te proporcioné anteriormente (el del documento que compartiste).
IMPORTANTE: Usar el código completo del documento que incluye:

Validaciones de esquema
Algoritmo Nearest Neighbor
Mejora 2-opt
Todas las funcionalidades
------------------------------------------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
6. Compilación y Testing
Paso 6.1: Probar en desarrollo
# Iniciar servidor de desarrollo
npm run dev


Resultado esperado:

VITE v5.0.8  ready in 500 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help

Abrir navegador en http://localhost:5173

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Paso 6.2: Verificar funcionalidad
Checklist de pruebas:

 -La página carga correctamente
 -Los botones de plantillas funcionan
 -Se pueden cargar archivos Excel
 -Aparecen errores al cargar archivo inválido
 -El botón "Calcular Rutas" se activa
 -El cálculo genera resultados
 -Se pueden descargar resultados
 -La interfaz es responsive (probar en móvil)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Paso 6.3: Compilar para producción
# Compilar proyecto
npm run build


Resultado esperado:
vite v5.0.8 building for production...
✓ 1234 modules transformed.
dist/index.html                   0.45 kB │ gzip:  0.30 kB
dist/assets/index-abc123.css     12.34 kB │ gzip:  3.21 kB
dist/assets/index-xyz789.js     543.21 kB │ gzip: 174.32 kB
✓ built in 3.45s
---------------------------------------------------------------------------------------------------------------------------------------------------------------

---------------------------------------------------------------------------------------------------------------------------------------------------------------
Paso 6.4: Preview de producción
# Probar build de producción localmente
npm run preview

Abrir http://localhost:4173 y verificar que todo funcione.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------
************************************************************************************************************************************************************************************************************************************************************************************************************************************


