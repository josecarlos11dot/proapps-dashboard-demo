# ProApps Carwash — Dashboard de operación (demo)

Sistema que uso todos los días para operar mi autolavado: cada auto que llega se registra, aparece al instante en el dashboard, se le asigna lavador y paquete, y al final del día el sistema genera el corte de caja y las comisiones de cada lavador.

> **Esta es la versión demo.** Usa una base de datos separada con datos ficticios (placas con prefijo `DMO`, lavadores y montos inventados) que se reinicia cada noche. Puedes registrar placas, asignar lavadores, editar precios y generar cortes sin afectar la operación real.

**Demo en vivo:** _agrega aquí el enlace de Vercel_
**Backend (API):** repositorio `proapps-backend-demo`

---

## El problema

El registro de autos se hacía en una computadora fija. Eso obligaba a tener a una persona dedicada solo a capturar, y en horas de mucha demanda se perdían registros, lo que afectaba el cobro y el cálculo de comisiones de los lavadores.

## Mi rol

Proyecto individual. Detecté el problema como dueño del negocio, definí los requerimientos y diseñé, desarrollé y desplegué tanto el frontend como el backend.

## Qué hace

- **Registro y fila de espera:** cada auto entra con su placa (o con foto si aún no se identifica la placa) y aparece en tiempo real como tarjeta en espera.
- **Asignación de servicio:** se elige color, tamaño (auto, SUV, XL), paquete y lavador; el precio se calcula con la tabla de precios guardada en la base de datos.
- **Historial por cliente:** al registrar una placa, el sistema indica si es cliente nuevo o cuántas visitas tiene. También se puede buscar el historial por placa o por modelo.
- **Corte de caja:** genera el resumen del día (servicios, caja, cortesías, servicios de taller) y la comisión de cada lavador. Si nadie lo genera, se hace solo a medianoche.
- **Reportes:** comparación de la semana y el mes contra el periodo anterior.
- **Configuración:** alta y baja de lavadores, edición de precios, nómina e historial de cortes.

## Decisiones técnicas

- **Socket.io para tiempo real.** Varias personas usan el sistema a la vez (quien recibe el auto y quien asigna el servicio). En lugar de que cada pantalla consulte al servidor cada cierto tiempo, el backend emite eventos (`nuevo-registro`, `registro-actualizado`, `registro-editado`, `registro-cancelado`) y todas las pantallas se actualizan al instante.
- **Corte automático con node-cron.** Si al final del día no se generaba el corte, los registros quedaban abiertos. Una tarea programada a medianoche (hora de México) cierra el día, calcula totales y comisiones y marca los servicios como completados, evitando cortes duplicados.
- **Precios en la base de datos.** Los precios cambian, así que se guardan en MongoDB y se editan desde el dashboard sin tocar código.
- **Validación de duplicados.** Si una placa ya está en servicio ese día, el sistema avisa con su folio en lugar de crear un segundo registro.
- **MongoDB con Mongoose.** El modelo de datos (registros, vehículos, lavadores, precios, cortes) cambió varias veces mientras usaba el sistema en el negocio; un esquema flexible me permitió iterar rápido.
- **Ambientes separados para la demo.** Esta versión lee la URL del backend desde variables de entorno (`VITE_API_URL`) y usa su propia base de datos, para poder mostrar el sistema sin exponer datos de clientes ni poner en riesgo la operación diaria.

## Resultado

El sistema está en producción y se usa a diario en el autolavado. Desde que lo usamos dejamos de perder registros y ya no necesitamos a una persona dedicada solo a operar el sistema de registro.

## Qué mejoraría después

- **Autenticación con roles** (encargado, cajero, administrador) para proteger la configuración y los cortes.
- **Una sola fuente de precios.** Hoy la tabla de precios vive en la base de datos, pero también hay copias en el frontend y en el cálculo del corte; centralizarla evitaría diferencias.
- **Pruebas automatizadas** para el cálculo de cortes y comisiones, que es la parte más sensible del sistema.
- **Migrar a TypeScript** para detectar errores de tipos en los datos que viajan entre frontend y backend.

## Tecnologías

React 18 · Vite · Socket.io · Lucide React · Node.js · Express · MongoDB (Mongoose) · node-cron · Vercel · Railway

## Correr en local

```bash
npm install
cp .env.example .env   # define VITE_API_URL con la URL del backend
npm run dev
```

| Variable | Descripción |
|---|---|
| `VITE_API_URL` | URL del backend (por ejemplo, el de demo en Railway) |
| `VITE_DEMO_MODE` | `true` para mostrar el aviso de modo demo |
# proapps-dashboard-demo
