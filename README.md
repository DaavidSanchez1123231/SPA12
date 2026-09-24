# NOVA Beauty Salon – Backend (Sprint 1: HU-02 y HU-01)

API REST con Spring Boot 3 (Java 17), Spring Security + JWT, JPA y PostgreSQL. El esquema lo crea Flyway (`V1__init.sql`).

## Levantar el proyecto

```bash
docker compose up -d        # PostgreSQL en localhost:5432 (bd/usuario/clave: nova)
mvn spring-boot:run         # API en http://localhost:8080
```

Usuarios de prueba (se crean solos la primera vez): `admin / Admin123!` y `recepcion / Recepcion123!`.
Cambiar con `SEED_ADMIN_PASSWORD` y `SEED_RECEPCION_PASSWORD`, y definir `JWT_SECRET` (Base64, mínimo 256 bits) fuera de desarrollo.

## Endpoints

| Método | Ruta | Acceso | HU |
|---|---|---|---|
| POST | `/api/auth/login` | Público | HU-02 |
| GET | `/api/auth/me` | Autenticado | HU-02 |
| GET | `/api/admin/ping` | Solo ADMINISTRADOR (temporal, para probar el 403) | HU-02 |
| GET | `/api/clientes?q=` | ADMINISTRADOR, RECEPCIONISTA | HU-01 |
| GET | `/api/clientes/{id}` | ADMINISTRADOR, RECEPCIONISTA | HU-01 |
| POST | `/api/clientes` | ADMINISTRADOR, RECEPCIONISTA | HU-01 |
| PUT | `/api/clientes/{id}` | ADMINISTRADOR, RECEPCIONISTA | HU-01 |

## Pruebas rápidas con curl

```bash
# Login
TOKEN=$(curl -s -X POST localhost:8080/api/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"nombreUsuario":"recepcion","contrasena":"Recepcion123!"}' | jq -r .token)

# Registrar cliente (201)
curl -i -X POST localhost:8080/api/clientes -H "Authorization: Bearer $TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"nombre":"Ana Torres","telefono":"987654321","correo":"ana@mail.com"}'

# Validación (400): nombre vacío y teléfono inválido
curl -i -X POST localhost:8080/api/clientes -H "Authorization: Bearer $TOKEN" \
  -H 'Content-Type: application/json' -d '{"nombre":"","telefono":"12"}'

# Recepcionista en endpoint de administrador (403)
curl -i localhost:8080/api/admin/ping -H "Authorization: Bearer $TOKEN"

# Sin token (401)
curl -i localhost:8080/api/clientes
```
