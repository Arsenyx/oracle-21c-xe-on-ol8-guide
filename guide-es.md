
# Configuración del Entorno de Aprendizaje SQL para la Preparación del Examen 1Z0-071

Guía completa para instalar Oracle Database 21c XE, SQL Developer y configurar un entorno de aprendizaje en Oracle Linux 8

## 📌 Para quién es esta guía

- Estudiantes que se preparan para el examen 1Z0-071 (Oracle Database SQL)
- Desarrolladores que necesitan una base de datos Oracle local para practicar
- Aquellos que quieren usar Oracle Linux como sistema de escritorio diario

## 📊 Especificaciones completas: [todos los registros neofetch](test-env/neofetch-logs.md) — para quienes quieren ver la configuración detallada de cada máquina.

> 💡 **Importante**: Toda la configuración se realiza localmente, sin "servidores remotos" — solo tu propio hardware.

## 🔧 Requisitos del Sistema

- **SO**: Oracle Linux 8 (probado en versión 8.10)
- **RAM**: mínimo 4 GB (8 GB recomendado para trabajo cómodo)
- **Disco**: mínimo 16 GB de espacio libre
- **Java**: OpenJDK 1.8 o superior (se instalará más tarde)

> ⚠️ **Importante**: Instrucciones probadas solo en Oracle Linux 8. No probado en otras distribuciones (RHEL, AlmaLinux, Rocky Linux) — la configuración y los paquetes pueden diferir.

## 📦 Preparación del Sistema

### 1. Verificar el nombre del host (importante para el listener)

```bash
# Verificar nombre actual
$ hostname

# Si el nombre difiere de localhost — editar hosts
$ sudo nano /etc/hosts
```

Añadir o descomentar:

```
127.0.0.1   localhost localhost.localdomain
127.0.0.1   oracle8  oracle8.me   # reemplazar con tu nombre de host
```

### 2. Actualizar sistema e instalar paquetes básicos

```bash
$ cd ~
$ sudo dnf update -y
$ sudo dnf install wget nano -y
```

## 🗄️ Instalación de Oracle Database 21c XE

### 3. Instalar dependencias previas

```bash
$ sudo dnf install oracle-database-preinstall-21c -y
```

### 4. Descargar e instalar la base de datos

```bash
# Descargar paquete RPM
$ wget https://download.oracle.com/otn-pub/otn_software/db-express/oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm

# Instalar
$ sudo dnf install oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm -y
```

### 5. Iniciar servicio y habilitar inicio automático

```bash
$ sudo systemctl enable oracle-xe-21c
$ sudo systemctl start oracle-xe-21c
$ sudo systemctl status oracle-xe-21c   # verificar que todo está bien
```

## 🔑 Configuración Inicial de la Base de Datos

### 6. Configurar variables de entorno para el usuario

```bash
$ nano ~/.bash_profile
```

Añadir al final del archivo:

```bash
export ORACLE_HOME=/opt/oracle/product/21c/dbhomeXE
export ORACLE_SID=XE
export PATH=$ORACLE_HOME/bin:$PATH
```

Aplicar cambios:

```bash
$ source ~/.bash_profile
```

### 7. Configurar la base de datos (establecer contraseña)

```bash
$ sudo /etc/init.d/oracle-xe-21c configure
```

Durante la configuración se te pedirá establecer contraseña para los usuarios del sistema (SYS, SYSTEM, PDBADMIN). **¡Recuérdala!**

### 8. Añadir usuario al grupo dba

```bash
$ sudo usermod -aG dba $USER
# Cerrar sesión y volver a entrar para aplicar cambios de grupo
# o ejecutar:
$ newgrp dba
```

### 9. Probar conexión a la base de datos

```bash
$ sqlplus / as sysdba
```

## 👤 Crear Usuario de Aprendizaje

### 10. Cambiar al contenedor correcto (PDB)

```sql
SQL> SHOW CON_NAME;   -- debería ser CDB$ROOT
SQL> ALTER SESSION SET CONTAINER = XEPDB1;
SQL> SHOW CON_NAME;   -- ahora XEPDB1
```

### 11. Crear usuario student

```sql
SQL>CREATE USER student IDENTIFIED BY student
  DEFAULT TABLESPACE users
  TEMPORARY TABLESPACE temp
  QUOTA UNLIMITED ON users;
```

### 12. Otorgar privilegios necesarios

```sql
-- Privilegios básicos de conexión y trabajo
SQL>GRANT CONNECT, RESOURCE TO student;
-- o enfoque más moderno:
SQL>GRANT CREATE SESSION TO student;

-- Privilegios para crear objetos
SQL>GRANT CREATE VIEW, CREATE SEQUENCE, CREATE PROCEDURE TO student;
SQL>GRANT CREATE TRIGGER TO student;  -- si es necesario

-- Salir
SQL>EXIT
```

### 13. Probar conexión como student

```bash
$ sqlplus student/student@localhost:1521/XEPDB1
```

### 14. Crear tabla de prueba

```sql
SQL>CREATE TABLE test_table (
  id NUMBER PRIMARY KEY,
  name VARCHAR2(50),
  created_at DATE DEFAULT SYSDATE
);

SQL>INSERT INTO test_table (id, name) VALUES (1, 'Ivan');
SQL>INSERT INTO test_table (id, name) VALUES (2, 'Maria');
SQL>INSERT INTO test_table (id, name) VALUES (3, 'Antonio');

SQL>COMMIT;

SQL>SELECT * FROM test_table;

SQL>EXIT;
```

## ☕ Instalación de Java y SQL Developer

### 15. Instalar Java 17 (recomendado para últimas versiones de SQL Developer)

```bash
$ sudo dnf install java-17-openjdk java-17-openjdk-devel -y

# Verificar versión por defecto
$ sudo alternatives --config java   # seleccionar 17 si es necesario

# Establecer JAVA_HOME
$ echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk' >> ~/.bashrc
$ echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
$ source ~/.bashrc

# Verificar
$ java --version
```

### 16. Descargar e instalar SQL Developer

```bash
$ wget https://download.oracle.com/otn_software/java/sqldeveloper/sqldeveloper-24.3.1-347.1826.noarch.rpm
$ sudo dnf install sqldeveloper-24.3.1-*.rpm -y
```

### 17. Iniciar (prueba)

```bash
$ sqldeveloper&
```
![Start ORACLE SQL Developer](images/Download%20SQL%20DEveloper.png)

## 🔌 Configuración de Conexión en SQL Developer

![Usage SQL Developer](images/Usage%20Oracle%20%20Tracking.png)

Hacer clic en el botón verde "+" (New Connection)
Rellenar los campos:

| Campo | Valor |
|-------|-------|
| **Connection Name** | XE Student (cualquier nombre) |
| **Username** | student |
| **Password** | student |
| **Save Password** | ✓ (opcional) |
| **Connection Type** | Basic |
| **Hostname** | localhost (o la IP de tu máquina) |
| **Port** | 1521 |
| **Service Name** | XEPDB1 |

![New Connect](images/New%20Connection.png)

Hacer clic en **Test** — debería mostrar "Success"
Hacer clic en **Save**, luego en **Connect**

### Verificación

En la ventana abierta de SQL Developer ejecutar:

```sql
SELECT * FROM test_table;
```

Hacer clic en la flecha verde (F5). Deberías ver:

```
ID | NAME    | CREATED_AT
---|---------|------------------
1  | Ivan    | 27-FEB-26
2  | Maria   | 27-FEB-26
3  | Antonio | 27-FEB-26
```

![Testing ORACLE SQL Developer](images/Oracle%20SQL%20Developer.png)

## 🐛 Errores Comunes y Soluciones

### ❌ ORA-12541: TNS:no listener

**Síntoma**: Error al conectar con `sqlplus student/student@localhost:1521/XEPDB1`

**Solución**:

1. Cambiar al usuario oracle:
```bash
$ sudo su - oracle
```

2. Verificar configuración del listener:
```bash
# Añadir variables de entorno para usuario oracle (si no están ya)
$ echo "export ORACLE_HOME=/opt/oracle/product/21c/dbhomeXE" >> ~/.bash_profile
$ echo "export ORACLE_SID=XE" >> ~/.bash_profile
$ echo "export PATH=$ORACLE_HOME/bin:$PATH" >> ~/.bash_profile
$ source ~/.bash_profile

# Verificar estado
$ lsnrctl status
```

3. **Importante**: ¡Prestar atención al PUERTO en la salida! En mi caso era 1539, no 1521:
```
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=ol8)(PORT=1539)))
```

4. Verificar configuración listener.ora:
```bash
$ cat $ORACLE_HOME/network/admin/listener.ora
```

5. Si el puerto es diferente, conectar usando ese puerto:
```bash
$ sqlplus student/student@localhost:1539/XEPDB1   # en lugar de 1521
```

6. O reiniciar el listener:
```bash
$ lsnrctl stop
$ lsnrctl start
# o
$ lsnrctl reload
```

> 💡 **Consejo**: Si cambias el puerto en listener.ora, no olvides actualizar las conexiones en SQL Developer y reiniciar el listener.

## ✅ ¿Qué Sigue?

Ahora tienes un entorno completamente funcional para:

- Preparación del examen 1Z0-071
- Aprendizaje de Oracle SQL
- Desarrollo de aplicaciones con base de datos Oracle

Todo funciona localmente, sin internet, en tu propio hardware. Tu familia puede seguir usando el ordenador para tareas diarias — Oracle Linux funciona perfectamente como estación de trabajo.

## 📚 Enlaces Útiles

- [Documentación Oficial de Oracle Database 21c XE](https://docs.oracle.com/en/database/oracle/oracle-database/21/xeinl/)
- [Especificación del Examen 1Z0-071](https://education.oracle.com/oracle-database-sql/pexam_1Z0-071)
- [Documentación de SQL Developer](https://docs.oracle.com/en/database/oracle/sql-developer/)

---

*¿Encontraste un error? ¿Tienes preguntas? ¡Abre un issue en el repositorio o inicia una discusión!*
```


Готово! Теперь у тебя трёхъязычный репозиторий 🇷🇺🇬🇧🇪🇸
