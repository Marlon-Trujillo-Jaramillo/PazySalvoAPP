# Proyecto 1 - Pasos Iniciales

## Crear proyecto

1. Crear solución en blanco: **PazYSalvoAPP**

## Crear Capas

### Capa de presentación, Capa del Modelo de datos, Datos y Lógica de negocio

1. Clic derecho en la solución
2. Agregar, opción Nuevo Proyecto
3. Seleccionar la opción de proyecto biblioteca de clases: 
![](./imagenes/img2.png)
4. Nombrar de la siguiente manera: Proyecto.Models, Proyecto.DLL, Proyecto.BLL

Para la capa de presentación, se creará un proyecto MVC .NET Core.
![](./imagenes/img1.png)

### Capa Datos - Configuraciones Iniciales

Esta capa nos permitirá la conexión a la BD y la utilización de recursos. Para esto, instalaremos y utilizaremos los siguientes paquetes:

1. Clic derecho en capa datos, **Definir proyecto como proyecto de inicio**
2. Clic derecho en capa datos, Administrar paquetes Nuget
3. Instalaremos las siguientes librerías:
    * **EntityFrameWorkCore:** biblioteca principal de Entity Framework Core. Proporciona funcionalidades básicas de mapeo objeto-relacional (ORM) para trabajar con bases de datos en aplicaciones .NET Core.
    * **EntityFrameWorkCore.SqlServer:** proveedor específico de base de datos para SQL Server en Entity Framework Core. Permite interactuar con bases de datos SQL Server de manera eficiente.
    * **EntityFrameWorkCore.Tools:** herramientas de línea de comandos para Entity Framework Core. Útiles para tareas como la creación de migraciones de base de datos, la aplicación de migraciones y la generación de clases de contexto de base de datos.

Una vez instalados los paquetes, agregaremos unos cuantos recursos más de forma manual:

1. Crear una carpeta para el contexto:
    * **Contexto:** representa una sesión de trabajo con la base de datos, donde se pueden realizar consultas, insertar, actualizar y eliminar registros.
2. Establecer esta capa como proyecto de inicio.
3. Enlazar nuestro modelo de datos a nuestra BD:
    * `Scaffold-DbContext "Server=(local); DataBase=PazYSalvo; Integrated Security=true" Microsoft.EntityFrameworkCore.SqlServer -OutputDir context`
    * `Scaffold-DbContext "Data Source=DESKTOP-88MTFLQ\SQLEXPRESS;Initial Catalog=PazSalvo;Integrated Security=true;Encrypt=True;TrustServerCertificate=true;" Microsoft.EntityFrameworkCore.SqlServer -OutPutDir Context`

Una vez establecida nuestra conexión, podremos visualizar el contexto asociado a nuestro modelo de datos:

```cs
using System;
using System.Collections.Generic;
using Microsoft.EntityFrameworkCore;
using PazYSalvoAPP.Models;

namespace PazYSalvoAPP.Models;

public partial class PazSalvoContext : DbContext
{
    public PazSalvoContext()
    {
    }

    public PazSalvoContext(DbContextOptions<PazSalvoContext> options)
        : base(options)
    {
    }

    public virtual DbSet<Cliente> Clientes { get; set; }

    public virtual DbSet<DetallesDeFactura> DetallesDeFacturas { get; set; }

    public virtual DbSet<Estado> Estados { get; set; }

    public virtual DbSet<Factura> Facturas { get; set; }

    public virtual DbSet<MediosDePago> MediosDePagos { get; set; }

    public virtual DbSet<Pago> Pagos { get; set; }

    public virtual DbSet<Persona> Personas { get; set; }

    public virtual DbSet<Role> Roles { get; set; }

    public virtual DbSet<Servicio> Servicios { get; set; }

    public virtual DbSet<Usuario> Usuarios { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Cliente>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Clientes__3214EC0768B1D232");

            entity.Property(e => e.FechaDeCreacion)
                .HasDefaultValueSql("(getdate())")
                .HasColumnType("datetime");

            entity.HasOne(d => d.Persona).WithMany(p => p.Clientes)
                .HasForeignKey(d => d.PersonaId)
                .HasConstraintName("FK__Clientes__Person__59FA5E80");

            entity.HasOne(d => d.Rol).WithMany(p => p.Clientes)
                .HasForeignKey(d => d.RolId)
                .HasConstraintName("FK__Clientes__RolId__5AEE82B9");
        });

        modelBuilder.Entity<DetallesDeFactura>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Detalles__3214EC07DE5B9E42");

            entity.Property(e => e.FechaDeCreacion)
                .HasDefaultValueSql("(getdate())")
                .HasColumnType("datetime");

            entity.HasOne(d => d.Factura).WithMany(p => p.DetallesDeFacturas)
                .HasForeignKey(d => d.FacturaId)
                .HasConstraintName("FK__DetallesD__Factu__6E01572D");

            entity.HasOne(d => d.Pago).WithMany(p => p.DetallesDeFacturas)
                .HasForeignKey(d => d.PagoId)
                .HasConstraintName("FK__DetallesD__PagoI__6EF57B66");
        });

        modelBuilder.Entity<Estado>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Estados__3214EC07800A561E");

            entity.Property(e => e.Descripcion)
                .HasMaxLength(255)
                .IsUnicode(false);
            entity.Property(e => e.Nombre)
                .HasMaxLength(25)
                .IsUnicode(false);
        });

        modelBuilder.Entity<Factura>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Facturas__3214EC07BC5E51CA");

            entity.Property(e => e.FechaDeCreacion)
                .HasDefaultValueSql("(getdate())")
                .HasColumnType("datetime");
            entity.Property(e => e.Saldo)
                .HasDefaultValueSql("((0))")
                .HasColumnType("decimal(10, 2)");

            entity.HasOne(d => d.Cliente).WithMany(p => p.Facturas)
                .HasForeignKey(d => d.ClienteId)
                .HasConstraintName("FK__Facturas__Client__619B8048");

            entity.HasOne(d => d.Estado).WithMany(p => p.Facturas)
                .HasForeignKey(d => d.EstadoId)
                .HasConstraintName("FK__Facturas__Estado__6477ECF3");

            entity.HasOne(d => d.MedioDePago).WithMany(p => p.Facturas)
                .HasForeignKey(d => d.MedioDePagoId)
                .HasConstraintName("FK__Facturas__MedioD__6383C8BA");

            entity.HasOne(d => d.ServicioAdquirido).WithMany(p => p.Facturas)
                .HasForeignKey(d => d.ServicioAdquiridoId)
                .HasConstraintName("FK__Facturas__Servic__628FA481");
        });

        modelBuilder.Entity<MediosDePago>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__MediosDe__3214EC072DC27202");

            entity.ToTable("MediosDePago");

            entity.Property(e => e.Descripcion).HasMaxLength(255);
            entity.Property(e => e.FechaDeCreacion)
                .HasDefaultValueSql("(getdate())")
                .HasColumnType("datetime");
            entity.Property(e => e.Nombre).HasMaxLength(100);
        });

        modelBuilder.Entity<Pago>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Pagos__3214EC078704C71A");

            entity.Property(e => e.Activo).HasDefaultValueSql("((1))");
            entity.Property(e => e.FechaDeCreacion)
                .HasDefaultValueSql("(getdate())")
                .HasColumnType("datetime");
            entity.Property(e => e.MontoDePago)
                .HasDefaultValueSql("((0))")
                .HasColumnType("decimal(10, 2)");

            entity.HasOne(d => d.Factura).WithMany(p => p.Pagos)
                .HasForeignKey(d => d.FacturaId)
                .HasConstraintName("FK__Pagos__FacturaId__693CA210");
        });

        modelBuilder.Entity<Persona>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Personas__3214EC07959AA70C");

            entity.Property(e => e.CorreoElectronico).HasMaxLength(100);
            entity.Property(e => e.DocumentoIdentificacion).HasMaxLength(20);
            entity.Property(e => e.FechaDeCreacion)
                .HasDefaultValueSql("(getdate())")
                .HasColumnType("datetime");
            entity.Property(e => e.Nombres).HasMaxLength(100);
            entity.Property(e => e.Telefono).HasMaxLength(20);
        });

        modelBuilder.Entity<Role>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Roles__3214EC07897A08BD");

            entity.Property(e => e.Activo).HasDefaultValueSql("((0))");
            entity.Property(e => e.Descripcion).HasMaxLength(255);
            entity.Property(e => e.Nombre).HasMaxLength(100);
        });

        modelBuilder.Entity<Servicio>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Servicio__3214EC07B2C89F3E");

            entity.Property(e => e.FechaDeCreacion)
                .HasDefaultValueSql("(getdate())")
                .HasColumnType("datetime");
            entity.Property(e => e.Nombre).HasMaxLength(100);
            entity.Property(e => e.Precio)
                .HasDefaultValueSql("((0))")
                .HasColumnType("decimal(10, 2)");

            entity.HasOne(d => d.Estado).WithMany(p => p.Servicios)
                .HasForeignKey(d => d.EstadoId)
                .HasConstraintName("FK__Servicios__Estad__6754599E");
        });

        modelBuilder.Entity<Usuario>(entity =>
        {
            entity.HasKey(e => e.Id).HasName("PK__Usuarios__3214EC072BCC30AF");

            entity.Property(e => e.Clave).HasMaxLength(255);
            entity.Property(e => e.NombreDeUsuario).HasMaxLength(100);

            entity.HasOne(d => d.Persona).WithMany(p => p.Usuarios)
                .HasForeignKey(d => d.PersonaId)
                .HasConstraintName("FK__Usuarios__Person__5FB337D6");

            entity.HasOne(d => d.Rol).WithMany(p => p.Usuarios)
                .HasForeignKey(d => d.RolId)
                .HasConstraintName("FK__Usuarios__RolId__60A75C0F");
        });

        OnModelCreatingPartial(modelBuilder);
    }

    partial void OnModelCreatingPartial(ModelBuilder modelBuilder);
}

```
## Conexión entre Capas

La conexión entre capas es lo que nos permite proporcionar abstracción a nuestra aplicación, permitiendo referencias entre los proyectos y estableciendo una comunicación directa. La forma en que las capas deben comunicarse dependerá de la lógica del proyecto, pero como estándar, seguiremos estas referencias:

1. La capa de datos solo referenciará a `models`.
2. La capa de negocio referenciará tanto a la capa `data` como a `models`.
3. Finalmente, la capa de presentación referenciará a nuestra capa `business` y `models`.

## Configuración de la Cadena de Conexión

Desde la capa de presentación, abrimos el archivo `appsettings.json` para hacer los siguientes ajustes:

1. En `appsettings.json`, agregaremos la cadena de conexión inicialmente configurada:
    * `Data Source=MI_SERVIDOR;Initial Catalog=PazSalvo;Integrated Security=true;Encrypt=True;TrustServerCertificate=true;`
2. Eliminamos del archivo de nuestro contexto la configuración de conexión que está dentro del siguiente método:
    * `protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)`
3. En `program.cs`, inyectamos la referencia de la cadena de conexión de la siguiente manera:
    ```cs
        // program.cs
        // Cadena de conexión
        builder.Services.AddDbContext<PazSalvoContext>( c =>
        {
            c.UseSqlServer(builder.Configuration.GetConnectionString("connString"));
        });
    ```

## Configuración de la Capa de Acceso a Datos

Primero, crearemos una nueva carpeta para nuestro repositorio, donde definiremos las operaciones a gestionar:

Aquí crearemos una interfaz llamada `IGenericRepository` que define un conjunto de métodos para realizar operaciones CRUD (Crear, Leer, Actualizar, Eliminar) genéricas en un repositorio de datos, donde `T` representa el tipo de entidad que se manejará en el repositorio.

1. `public interface IGenericRepository<T> where T : class`: Declara una interfaz pública llamada IGenericRepository que toma un parámetro de tipo genérico T. La interfaz está restringida a tipos de referencia (where T : class), lo que significa que T debe ser una clase y no un tipo de valor.

    ```cs
    namespace PazYSalvoAPP.Data.Repositories
    {
        // Declaración de la interfaz IGenericRepository<T>
        public interface IGenericRepository<T> where T : class
        {
            // Método para insertar un objeto de tipo T en la base de datos
            Task<bool> Insertar(T model);

            // Método para actualizar un objeto de tipo T en la base de datos
            Task<bool> Actualizar(T model);

            // Método para leer un objeto de tipo T de la base de datos mediante su identificador único
            Task<T> Leer(int id);

            // Método para leer todos los objetos de tipo T de la base de datos
            // Devuelve una consulta IQueryable, que permite realizar operaciones de consulta sobre la colección de objetos de tipo T
            Task<IQueryable> LeerTodos();

            // Método para eliminar un objeto de tipo T de la base de datos mediante su identificador único
            Task<bool> Eliminar(int id);
        }
    }
    ```

2. Configura cada una de tus entidades para que hereden de esta interfaz, como se muestra en el siguiente ejemplo:

    ```cs
    using Microsoft.EntityFrameworkCore;
    using PazYSalvoAPP.Data.Context;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace PazYSalvoAPP.Data.Repositories
    {
        public class FacturaRepository : IGenericRepository<Factura>
        {
            // Establecer conexión
            private readonly PazSalvoContext _context;
            public FacturaRepository(PazSalvoContext context)
            {
                _context = context;
            }

            public async Task<bool> Actualizar(Factura model)
            {
                bool result = default;
                try
                {
                    _context.Facturas.Update(model);
                    await _context.SaveChangesAsync();

                    return !result;
                }
                catch (Exception)
                {
                    return result;
                }
            }

            public async Task<bool> Eliminar(int id)
            {
                bool result = default;
                var factura = _context.Facturas.FirstOrDefault(f => f.Id == id);
                if (factura == null) return result;

                try
                {
                    _context.Facturas.Remove(factura);
                    await _context.SaveChangesAsync();

                    return !result;
                }
                catch (Exception)
                {
                    return result;
                }
            }

            public async Task<bool> Insertar(Factura model)
            {
                bool result = default;
                try
                {
                    _context.Facturas.Add(model);
                    await _context.SaveChangesAsync();

                    return !result;
                }
                catch (Exception)
                {
                    return result;
                }
            }

            public async Task<Factura> Leer(int id)
            {
                if (id == 0) return null;
                var factura = await _context.Facturas.FirstAsync(f => f.Id == id);
                return factura;   
            }

            public async Task<IQueryable> LeerTodos()
            {
                IQueryable<Factura> listaDeFacturas = _context.Facturas;
                return listaDeFacturas;
            }
        }
    }
    ```

La clase `FacturaRepository` implementa todos los métodos definidos en la interfaz `IGenericRepository<Factura>`. Cada método realiza una operación específica en la base de datos relacionada con las facturas.

## Capa de Lógica de Negocio

1. Crearemos una carpeta de servicios (`services`).
2. Crearemos una interfaz por cada modelo, donde se definirá el comportamiento de cada uno.
3. Finalmente, crearemos una clase asociada a cada una, donde aplicaremos la lógica necesaria para realizar las distintas operaciones de negocio.

    * **Definición de la clase:** La clase `FacturaService` se encuentra en el espacio de nombres `PazYSalvoAPP.Business.Services`, lo que indica su función en la capa de lógica de negocio. Implementa la interfaz `IFacturaService`, que contiene las diferentes operaciones que esta podrá realizar.
    * **Uso del repositorio:** El servicio delega la lógica de acceso a datos a la instancia del repositorio inyectado (`_modelRepository`). Esta separación de responsabilidades promueve la mantenibilidad y la capacidad de prueba del código.

    ```cs
    using PazYSalvoAPP.Data.Context;
    using PazYSalvoAPP.Data.Repositories;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace PazYSalvoAPP.Business.Services
    {
        public class FacturaService : IFacturaService
        {
            private readonly IGenericRepository<Factura> _modelRepository;

            public FacturaService(IGenericRepository<Factura> modelRepository)
            {
                _modelRepository = modelRepository;
            }

            public async Task<bool> Actualizar(Factura model)
            {
                return await _modelRepository.Actualizar(model);
            }

            public async Task<bool> Eliminar(int id)
            {
                throw new NotImplementedException();
            }

            public async Task<bool> Insertar(Factura model)
            {
                return await _modelRepository.Insertar(model);
            }

            public async Task<Factura> Leer(int id)
            {
                return await _modelRepository.Leer(id);
            }

            public async Task<IQueryable<Factura>> LeerTodos()
            {
                return await _modelRepository.LeerTodos();
            }

            public async Task<Factura> ObtenerFacturasPorServicio(int id)
            {
                var facturasPorServicio = await _modelRepository.LeerTodos();
                Factura factura = facturasPorServicio.Where(f => f.ServicioAdquiridoId == id).FirstOrDefault();

                return factura;
            }
        }
    }
    ```
