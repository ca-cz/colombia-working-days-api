# Colombia Working Days API

API REST para calcular dias y horas habiles en Colombia, considerando festivos nacionales, horarios laborales y conversion de zona horaria local a UTC.

[![TypeScript](https://img.shields.io/badge/TypeScript-5.2-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?style=flat-square&logo=nodedotjs&logoColor=white)](https://nodejs.org/)
[![Test Coverage](https://img.shields.io/badge/Test_Coverage-91.97%25-success?style=flat-square)](https://github.com/ca-cz/colombia-working-days-api)
[![Tests Passing](https://img.shields.io/badge/Tests-48%2F48_passing-success?style=flat-square)](https://github.com/ca-cz/colombia-working-days-api)
[![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](LICENSE)

---

## Enlaces

- **API en Produccion**: [https://colombia-working-days-api.vercel.app](https://colombia-working-days-api.vercel.app)
- **Repositorio GitHub**: [https://github.com/ca-cz/colombia-working-days-api](https://github.com/ca-cz/colombia-working-days-api)
- **Health Check**: [https://colombia-working-days-api.vercel.app/health](https://colombia-working-days-api.vercel.app/health)

---

## Caracteristicas

- Calculo deterministico de dias y horas habiles.
- Matriz de festivos colombianos integrada (Ley Emiliani, 2025-2035).
- Horario laboral bancario/corporativo: Lunes a Viernes 8:00 - 17:00 (receso de almuerzo: 12:00 - 13:00).
- Manejo estricto de zona horaria: conversion bidireccional automatica Colombia (America/Bogota, UTC-5) a UTC.
- Validacion tipada y exhaustiva de parametros de entrada.
- Desarrollado en TypeScript bajo modo estricto.
- Suite de pruebas unitarias y de integracion con 91.97% de cobertura de codigo (48/48 tests superados).

---

## Instalacion y Ejecucion Local

### Prerrequisitos
- Node.js >= 18.0
- npm o yarn

### Configuracion

```bash
# 1. Clonar el repositorio
git clone https://github.com/ca-cz/colombia-working-days-api.git
cd colombia-working-days-api

# 2. Instalar dependencias
npm install

# 3. Iniciar en modo desarrollo
npm run dev
```

El servidor quedara disponible en: `http://localhost:3001`

### Pruebas de Funcionamiento Local

**Via curl:**
```bash
# Health check
curl http://localhost:3001/health

# Sumar 1 dia habil
curl "http://localhost:3001/api/working-days?days=1"

# Sumar 4 horas habiles
curl "http://localhost:3001/api/working-days?hours=4"

# Fecha especifica en formato ISO 8601 UTC
curl "http://localhost:3001/api/working-days?days=2&hours=3&date=2025-01-15T13:00:00.000Z"
```

### Build y Ejecucion de Produccion

```bash
npm run build
npm start
```

---

## Uso de la API

### Endpoint Principal

```http
GET /api/working-days
```

### Parametros Query

| Parametro | Tipo | Requerido | Descripcion |
| :--- | :--- | :--- | :--- |
| `days` | `number` | Condicional* | Dias habiles a sumar (entero entre 0 y 365). |
| `hours` | `number` | Condicional* | Horas habiles a sumar (entero entre 0 y 2000). |
| `date` | `string` | Opcional | Fecha base en formato ISO 8601 UTC con sufijo `Z`. Por defecto: fecha actual. |

*\* Al menos uno de los dos parametros (`days` o `hours`) es requerido.*

### Ejemplos de Respuesta

#### Respuesta Exitosa (200 OK)
```json
{
  "date": "2025-01-16T15:00:00.000Z"
}
```

#### Error de Parametros (400 Bad Request)
```json
{
  "error": "InvalidParameters",
  "message": "At least one parameter (days or hours) is required"
}
```

```json
{
  "error": "InvalidParameters",
  "message": "Date must be in ISO 8601 format with Z suffix"
}
```

#### Endpoint No Encontrado (404 Not Found)
```json
{
  "error": "NotFound",
  "message": "Endpoint not found"
}
```

---

## Reglas de Negocio

| Regla | Descripcion |
| :--- | :--- |
| **Dias habiles** | Lunes a viernes, excluyendo fines de semana y festivos oficiales en Colombia. |
| **Horario laboral** | 8:00 AM - 5:00 PM (ventana de receso: 12:00 PM - 1:00 PM). Total: 8 horas habiles por dia. |
| **Zona horaria** | Entrada normalizada a America/Bogota (UTC-5); calculos en hora local; salida en UTC. |
| **Normalizacion** | Fechas fuera de horario se ajustan al inicio de la siguiente jornada laboral valida. |
| **Secuencia de calculo** | Primero se acumulan los dias habiles y posteriormente las horas habiles. |
| **Festivos integrados** | Estructura en memoria con 178 festivos oficiales verificados (2025-2035). |

---

## Pruebas y Cobertura

La suite de pruebas fue construida con Jest y Supertest, alcanzando mas del 91% de cobertura de ramas y sentencias.

```bash
# Ejecutar todas las pruebas
npm test

# Modo observador (watch)
npm run test:watch

# Reporte detallado de cobertura
npm run test:coverage
```

### Estructura de Pruebas

```text
tests/
└── unit/
    ├── handlers/           # Controladores HTTP y validacion de entrada
    ├── services/           # Logica central de calculo y navegacion de calendario
    ├── utils/              # Funciones auxiliares de fecha y zonas horarias
    └── integration.test.ts # Pruebas end-to-end sobre endpoints REST
```

---

## Stack Tecnologico

| Componente | Tecnologia | Version | Proposito |
| :--- | :--- | :--- | :--- |
| **Runtime** | Node.js | >= 18.0 | Entorno de ejecucion |
| **Lenguaje** | TypeScript | ^5.2.2 | Tipado estatico estricto |
| **Framework HTTP** | Express.js | ^4.18.2 | Servidor minimalista de alto rendimiento |
| **Manejo de Fechas** | date-fns-tz | ^2.0.0 | Manipulacion inmutable de zonas horarias IANA |
| **Testing** | Jest | ^29.7.0 | Runner y aserciones de pruebas |
| **Test de Integracion** | Supertest | ^6.3.3 | Simulacion de peticiones HTTP |

### Decisiones de Arquitectura

- **Inmutabilidad de Fechas**: Se selecciono `date-fns-tz` para evitar mutaciones de estado y optimizar el empaquetado final (tree-shaking).
- **Lookup O(1) de Festivos**: Uso de estructuras basadas en `Set` para consulta de festivos en tiempo constante sin latencia de I/O a base de datos.
- **Separacion por Capas**: Desacople estricto entre controladores HTTP, servicios de dominio y utilidades puras.
- **Limites de Proteccion Anti-DoS**: Restriccion superior explicita en los parametros `days` (max 365) y `hours` (max 2000).

---

## Despliegue

La API se encuentra empaquetada para despliegue en entornos Serverless y contenedores:

- **Vercel Serverless Functions**: Configuracion nativa en `vercel.json`.
- **Docker**: Compatible para despliegues en ECS, Cloud Run o Kubernetes.
- **AWS Lambda**: Adaptable mediante handlers estandar con Serverless Framework o AWS SAM.

---

## Contribucion

1. Realizar un Fork del repositorio: `https://github.com/ca-cz/colombia-working-days-api`
2. Crear una rama de trabajo: `git checkout -b feature/nueva-mejora`
3. Confirmar los cambios: `git commit -am 'feat: implementacion de nueva regla'`
4. Publicar la rama: `git push origin feature/nueva-mejora`
5. Abrir un Pull Request documentando los cambios.

---

## Licencia

Este proyecto esta licenciado bajo la Licencia MIT. Consulte el archivo [LICENSE](LICENSE) para mas detalles.
