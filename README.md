# 🤖 Auto-Job Application Bot: Prospección IT Automatizada con IA

[![n8n](https://img.shields.io/badge/n8n-Workflow_Automation-FF6D5A?style=flat-square&logo=n8n)](https://n8n.io/)
[![Gemini](https://img.shields.io/badge/Google_Gemini-LLM-4285F4?style=flat-square&logo=google)](https://deepmind.google/technologies/gemini/)
[![JavaScript](https://img.shields.io/badge/JavaScript-Data_Parsing-F7DF1E?style=flat-square&logo=javascript)](https://developer.mozilla.org/es/docs/Web/JavaScript)
[![ScraperAPI](https://img.shields.io/badge/ScraperAPI-Bypass-00C0F1?style=flat-square)](https://www.scraperapi.com/)

Un sistema *End-to-End* diseñado para automatizar el embudo completo de búsqueda de empleo en el sector tecnológico. La arquitectura integra web scraping avanzado, orquestación de flujos y modelos de lenguaje (LLMs) para analizar ofertas de trabajo y generar candidaturas (CVs y Cover Letters) hiper-personalizadas en tiempo real.

## 🎯 Objetivo del Proyecto

Eliminar la fricción manual en la prospección de empleo IT mediante un orquestador que analiza portales de empleo, filtra posiciones (ej. perfiles Junior/Prácticas), adapta el perfil técnico del candidato a los requisitos específicos de cada vacante y compila los entregables finales en PDF, notificando al usuario mediante una integración de correo electrónico.

## 🧠 Arquitectura del Flujo

El sistema está orquestado en **n8n (Self-Hosted)** y sigue un pipeline de datos secuencial:

1. **Extracción de Lista (List Scraping):** Rastreo inicial del portal de empleo aplicando parámetros de búsqueda (ubicación, rol, experiencia).
2. **Extracción de Detalle (Deep Scraping):** Iteración sobre cada vacante detectada (`Loop Over Items`) para extraer el árbol DOM completo de la oferta individual.
3. **LLM Formatting & Prompt Engineering:** Inyección del HTML extraído y el Currículum Base en Google Gemini. Mediante técnicas de *Prompt Engineering*, la IA extrae las tecnologías clave, adapta el extracto profesional, prioriza las *skills* coincidentes y redacta una Cover Letter disruptiva.
4. **PDF Generation:** Renderizado en memoria del código HTML generado por el LLM y conversión binaria a formato PDF mediante el motor interno de n8n.
5. **Email API:** Empaquetado de la Cover Letter en el cuerpo del mensaje y el CV en formato adjunto, distribuyéndolo vía API SMTP/Gmail al usuario final.

## 🛠️ Stack Tecnológico

*   **Orquestación:** n8n (Self-Hosted)
*   **Web Scraping / Proxy:** ScraperAPI
*   **Inteligencia Artificial:** Google Gemini (PaLM API)
*   **Lógica y Transformación de Datos:** JavaScript (ES6)
*   **Formatos y Estilos:** HTML5, CSS3, JSON

## ⚠️ Retos Técnicos Superados

Durante el desarrollo de esta arquitectura, se resolvieron bloqueos estructurales habituales en la automatización de procesos web:

*   **Evasión de protecciones Anti-Bot (Cloudflare):** El acceso directo a los endpoints del portal resultaba en errores `HTTP 403` y `404`. Se implementó un *Scraping Bridge* (ScraperAPI) con renderizado de JavaScript en servidores *headless* y rotación de IPs residenciales, burlando exitosamente los *challenges* de seguridad de Cloudflare.
*   **Fallo de sintaxis y alucinaciones en LLMs (JSON Parsing):** Los modelos de lenguaje tienden a inyectar caracteres no válidos (como Markdown residual ````json` o llaves de cierre adicionales `}\n}`) al estructurar salidas de datos complejas. Se desarrolló un script en JavaScript ejecutado pre-renderizado que utiliza expresiones regulares y manipulación de cadenas (`indexOf('{')`, `lastIndexOf('}')`) complementado con un bloque `Try-Catch`. Esto aísla matemáticamente el objeto JSON puro y evita fallos fatales de desincronización en el pipeline.
*   **Control de Cuotas (Rate Limiting):** Para respetar las cuotas de la API del LLM, se implementó control de flujo (*pacing*) mediante nodos de espera y funciones de filtrado rígido (`Math.min()`) sobre matrices de datos.

## ⚙️ Instalación / Importación

Para explorar la arquitectura de los nodos:
1. Clona este repositorio o descarga el archivo `workflow.json`.
2. En tu instancia de n8n, selecciona `Import from File` en el lienzo de trabajo.
3. Asegúrate de configurar tus propias credenciales en los nodos de **ScraperAPI**, **Google Gemini** y **Gmail/SMTP**.

---
*Desarrollado por [Álvaro Pérez-Moreno Callejo](https://alvaroje.github.io/portfolio/)*
