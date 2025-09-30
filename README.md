# Inventory PWA Demo

Una aplicación web progresiva (PWA) para gestión de inventario con backend Node.js y base de datos PostgreSQL en AWS RDS.

## 🚀 Características

- **Frontend**: PWA con HTML, CSS y JavaScript vanilla
- **Backend**: Node.js con Express
- **Base de datos**: PostgreSQL en AWS RDS
- **API REST**: Endpoints completos para CRUD de productos
- **Dashboard**: Estadísticas en tiempo real del inventario

## 📋 Prerrequisitos

- Node.js (versión 14 o superior)
- npm o yarn
- Cuenta de AWS con acceso a RDS
- Instancia de PostgreSQL en AWS RDS

## 🛠️ Instalación

1. **Clona el repositorio**
   ```bash
   git clone <tu-repositorio>
   cd ingsoftCharelli
   ```

2. **Instala las dependencias**
   ```bash
   npm install
   ```

3. **Configura las variables de entorno**
   ```bash
   cp .env.example .env
   ```

## 🔧 Configuración de AWS RDS PostgreSQL

### 1. Crear instancia RDS en AWS

1. Accede a la consola de AWS
2. Ve a **RDS** en el panel de servicios
3. Haz clic en **Create database**
4. Selecciona **PostgreSQL** como motor de base de datos
5. Configura los siguientes parámetros:

   ```
   DB instance identifier: inventory-db
   Master username: tu_usuario_admin
   Master password: tu_contraseña_segura
   DB instance class: db.t3.micro (para desarrollo)
   Storage: 20 GB
   ```

### 2. Configurar Security Groups

1. En la consola de EC2, ve a **Security Groups**
2. Crea un nuevo security group o modifica el existente
3. Añade una regla de entrada:
   ```
   Type: PostgreSQL
   Port: 5432
   Source: 0.0.0.0/0 (para desarrollo) o tu IP específica
   ```

### 3. Obtener información de conexión

Una vez creada la instancia RDS, necesitarás:

- **Endpoint**: `tu-instancia.region.rds.amazonaws.com`
- **Puerto**: `5432` (por defecto)
- **Nombre de la base de datos**: `postgres` (por defecto) o el que hayas creado
- **Usuario**: El que configuraste como master username
- **Contraseña**: La que configuraste como master password

## 🔐 Variables de Entorno

Crea un archivo `.env` en la raíz del proyecto con la siguiente configuración:

```env
# Configuración del servidor
PORT=3001

# Configuración de la base de datos AWS RDS
DB_HOST=tu-instancia.region.rds.amazonaws.com
DB_USER=tu_usuario_admin
DB_PASSWORD=tu_contraseña_segura
DB_NAME=postgres
DB_PORT=5432

# Configuración SSL (para producción)
DB_SSL=true
```

### Ejemplo de configuración:

```env
PORT=3001
DB_HOST=inventory-db.c123456789.us-east-1.rds.amazonaws.com
DB_USER=admin
DB_PASSWORD=MiContraseñaSegura123!
DB_NAME=inventory_db
DB_PORT=5432
DB_SSL=true
```

## 🚀 Ejecución

### Desarrollo
```bash
npm start
```

### Con nodemon (recarga automática)
```bash
npx nodemon server.js
```

El servidor se ejecutará en `http://localhost:3001`

## 📊 Estructura de la Base de Datos

La aplicación crea automáticamente la siguiente tabla:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  category TEXT NOT NULL,
  quantity INTEGER NOT NULL,
  price NUMERIC(12,2) NOT NULL,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

## 🔌 API Endpoints

### Productos
- `GET /api/products` - Obtener todos los productos
- `GET /api/products/:id` - Obtener un producto específico
- `POST /api/products` - Crear un nuevo producto
- `PUT /api/products/:id` - Actualizar un producto
- `DELETE /api/products/:id` - Eliminar un producto

### Estadísticas
- `GET /api/stats` - Obtener estadísticas del dashboard

### Ejemplo de creación de producto:
```bash
curl -X POST http://localhost:3001/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Laptop Gaming",
    "category": "Electronics",
    "quantity": 10,
    "price": 1599.99,
    "description": "Laptop para gaming de alta gama"
  }'
```

## 🔒 Consideraciones de Seguridad

### Para Desarrollo:
- El SSL está configurado con `rejectUnauthorized: false`
- Las credenciales se almacenan en variables de entorno

### Para Producción:
1. **Configura SSL apropiadamente**:
   ```javascript
   ssl: {
     rejectUnauthorized: true,
     ca: fs.readFileSync('path/to/ca-cert.pem')
   }
   ```

2. **Restringe el acceso en Security Groups**:
   - Solo permite conexiones desde tu IP o VPC
   - No uses `0.0.0.0/0` en producción

3. **Usa IAM Database Authentication** (recomendado):
   ```javascript
   ssl: { rejectUnauthorized: true },
   sslmode: 'require'
   ```

## 🐛 Solución de Problemas

### Error de conexión a la base de datos
1. Verifica que la instancia RDS esté ejecutándose
2. Confirma que el Security Group permite conexiones en el puerto 5432
3. Verifica las credenciales en el archivo `.env`

### Error SSL
```bash
# Para desarrollo, puedes deshabilitar temporalmente SSL
DB_SSL=false
```

### Error de timeout
- Verifica que tu IP esté en la lista blanca del Security Group
- Confirma que la instancia RDS esté en la misma región

## 📝 Logs y Monitoreo

La aplicación registra errores de base de datos en la consola. Para producción, considera usar:

- **CloudWatch** para logs de AWS RDS
- **Winston** o **Morgan** para logging de la aplicación
- **New Relic** o **DataDog** para monitoreo

## 🔄 Backup y Recuperación

### Backup automático
AWS RDS realiza backups automáticos por defecto. Configura:

- **Retention period**: 7 días (mínimo)
- **Backup window**: Durante horas de bajo tráfico
- **Maintenance window**: Para actualizaciones

### Backup manual
```bash
# Desde AWS CLI
aws rds create-db-snapshot \
  --db-instance-identifier inventory-db \
  --db-snapshot-identifier inventory-backup-$(date +%Y%m%d)
```

## 📈 Escalabilidad

### Opciones de escalado:
1. **Vertical**: Cambiar el tipo de instancia (db.t3.small, db.t3.medium, etc.)
2. **Horizontal**: Implementar read replicas
3. **Conexiones**: Usar connection pooling (ya implementado con `pg.Pool`)

## 🤝 Contribución

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia ISC.

## 📞 Soporte

Si tienes problemas con la configuración de AWS RDS:

1. Revisa la [documentación oficial de AWS RDS](https://docs.aws.amazon.com/rds/)
2. Consulta los logs de CloudWatch
3. Verifica la configuración de Security Groups
4. Confirma que la instancia esté en el estado "Available"

---

**Nota**: Este README asume que ya tienes una instancia de AWS RDS PostgreSQL configurada. Si necesitas ayuda con la creación de la instancia RDS, consulta la documentación oficial de AWS.
