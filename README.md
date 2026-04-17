# 🚀 VRP Route Optimizer - IA & Heuristic Solution

![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)

## 📋 Descripción General
Esta aplicación es una solución avanzada de **automatización logística** diseñada para resolver el problema de optimización de rutas de vehículos (VRP - Vehicle Routing Problem). Permite a las empresas transformar datos brutos de puntos de visita en rutas inteligentes, minimizando tiempos de traslado y costos operativos mediante algoritmos de inteligencia artificial heurística.

**Ideal para:** Equipos de ventas, visitadores médicos, logística de última milla y supervisión de campo.

---

## ✨ Características Principales
- **Automatización de Decisiones:** Asignación balanceada de puntos de visita (PDVs) según carga de trabajo y prioridad.
- **Motor de Optimización:** Implementación de algoritmos de IA clásica para la reducción de distancias.
- **Validación de Datos Inteligente:** Procesamiento de archivos Excel complejos con detección de errores en tiempo real (SheetJS).
- **Cálculo Geográfico Real:** Uso de la fórmula Haversine para precisión en coordenadas globales.
- **Exportación Profesional:** Generación de reportes operativos listos para ejecución en campo.

---

## 🧠 El Cerebro: IA & Heurística
El sistema no solo conecta puntos; aplica una lógica de tres fases para garantizar la eficiencia:

1. **Asignación:** Distribución basada en capacidad y restricciones operativas.
2. **Construcción (Nearest Neighbor):** Generación de una ruta inicial factible en tiempo real.
3. **Optimización (2-opt Improvement):** Un algoritmo de mejora local que elimina cruces en las rutas, reduciendo típicamente entre un 15% y 25% la distancia total recorrida.

> **IA-Assisted Development:** Este proyecto fue desarrollado utilizando metodologías de ingeniería asistida por IA (Claude/Cursor), permitiendo una optimización profunda del algoritmo de mejora local y un riguroso proceso de refactorización para garantizar un rendimiento de grado producción.

---

## 🛠️ Stack Tecnológico
- **Frontend:** React 18 + Vite (Máximo rendimiento y carga ultrarrápida).
- **Estilos:** Tailwind CSS (Diseño profesional y responsive).
- **Procesamiento de Data:** SheetJS (xlsx) para el manejo de estructuras de datos complejas en el cliente.
- **Iconografía:** Lucide React.

---

## 🚀 Instalación y Uso

### Requisitos
- Node.js 16.x o superior
- npm 8.x o superior

### Pasos para ejecución local
1. Clonar el repositorio:
   ```bash
   git clone [https://github.com/tu-usuario/optimizador-rutas-vrp.git](https://github.com/tu-usuario/optimizador-rutas-vrp.git)
Instalar dependencias:

Bash
npm install
Iniciar el servidor de desarrollo:

Bash
npm run dev
📂 Estructura del Proyecto
/src: Código fuente del motor de optimización y componentes de UI.

/docs: Documentación técnica profunda del algoritmo y manual de usuario.

/data: Plantillas de ejemplo para pruebas de carga.

👨‍💻 Autor
Gregory Antonio Abreu Marte Ingeniero de Software / Epsecialista IT 

📄 Licencia
Este proyecto está bajo la Licencia MIT - Mira el archivo LICENSE para más detalles.
