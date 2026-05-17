# 3.5 Modelo de Datos y Especificación de la API

Para que la infraestructura de microservicios tenga una utilidad real, se ha desarrollado un flujo de captación de información soportado por un modelo de datos relacional y un contrato de API RESTful.

---

## Esquema de Base de Datos (PostgreSQL)

La persistencia de los datos se centraliza en PostgreSQL. Se ha diseñado la tabla `clientes_potenciales` para almacenar la información de los *leads* captados a través del *frontend*.

| Columna | Tipo de dato | Restricciones / Descripción |
|---------|-------------|----------------------------|
| `id` | `INTEGER` | Clave primaria (PK). Identificador único autoincremental |
| `nombre` | `VARCHAR(255)` | Almacena el nombre completo del *lead* |
| `email` | `VARCHAR(255)` | Correo de contacto. Campo clave para el negocio |
| `empresa` | `VARCHAR(255)` | Entidad o empresa que representa el usuario |
| `fecha_solicitud` | `TIMESTAMP WITH TIME ZONE` | Registro temporal exacto. El uso de zonas horarias previene desfases si el *cluster* se despliega en regiones internacionales |

---

## Contrato de Comunicación (*Payload* JSON)

El *frontend* (Nginx) y el *backend* (Node.js) están completamente desacoplados. Para comunicarse utilizan un estándar estricto basado en JSON. Cuando un usuario envía el formulario web, el cliente genera una petición `HTTP POST` hacia el Ingress con la siguiente estructura de carga útil:

```json
{
  "nombre": "Juan Pérez",
  "email": "jperez@ejemplo.com",
  "empresa": "Tech Solutions SL"
}
```

El microservicio de Node.js recibe este JSON, lo valida y lo inyecta en la base de datos. Los campos `id` y `fecha_solicitud` no se envían desde el *frontend* por motivos de seguridad; es el propio motor de PostgreSQL quien los genera automáticamente mediante secuencias y valores por defecto en el momento de la inserción, garantizando que no puedan ser manipulados desde el navegador del cliente.

---

## Endpoints de la API REST

| Método | Ruta | Descripción | Código de respuesta |
|--------|------|-------------|---------------------|
| `GET` | `/health` | Comprobación de vida del servicio | `200 OK` |
| `GET` | `/db-check` | Verificación de conectividad con PostgreSQL | `200 OK` / `500 Error` |
| `POST` | `/leads` | Inserción de un nuevo *lead* en la base de datos | `201 Created` / `400 Bad Request` |

---

## Código fuente del *backend* (`src/backend/server.js`)

```javascript
// src/backend/server.js
const express = require('express');
const { Pool } = require('pg');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

const pool = new Pool({
  host:     process.env.DB_HOST     || 'postgres-service',
  user:     process.env.DB_USER     || 'postgres',
  password: process.env.POSTGRES_PASSWORD,
  database: process.env.DB_NAME     || 'postgres',
  port:     parseInt(process.env.DB_PORT || '5432'),
});

app.get('/health', (_req, res) => res.json({ status: 'ok' }));

app.get('/db-check', async (_req, res) => {
  try {
    await pool.query('SELECT 1');
    res.json({ status: 'ok', db: 'connected' });
  } catch (err) {
    res.status(500).json({ status: 'error', message: err.message });
  }
});

app.post('/leads', async (req, res) => {
  const { nombre, email, empresa } = req.body;
  if (!nombre || !email || !empresa) {
    return res.status(400).json({ message: 'nombre, email y empresa son obligatorios' });
  }
  try {
    await pool.query(
      'INSERT INTO clientes_potenciales (nombre, email, empresa) VALUES ($1, $2, $3)',
      [nombre, email, empresa]
    );
    res.status(201).json({ message: 'Lead registrado correctamente' });
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Backend escuchando en puerto ${PORT}`));
```

---

*Siguiente: [4. Anexos →](08-anexos.md)*
