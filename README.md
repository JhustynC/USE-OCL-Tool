
# Demo de la Herramienta USE: UML-based Specification Environment
#### Realizado por: Jhustyn Carvajal  
#### Fecha: 06/06/2025

---

## 1. Contenido

- `use-6.0.0.zip`: Instalador. 
- `model.use`: Definición del modelo UML en lenguaje USE.
- `usage.scenario.soil`: Script para la creación de instancias del modelo.

---

## 2. Pasos para Levantar el Proyecto

1. Descomprimir `use-6.0.0.zip` dentro del disco C:\

2. Dirigirse a `C:\use-6.0.0\bin`

1. Ejecutar USE:

```bash
# En Linux
./use

# En Windows
./use.bat (o doble click al archivo)
```

2. Cargar el archivo `model.use` desde la interfaz gráfica (opción *Open Specification* y seleccionar el archivo).
3. Desde la consola que proporciona el programa, ejecutar el archivo `usage.scenario.soil` mediante el comando:

```bash
use> open *path_del_archivo*/usage.scenario.soil
```

4. Realizar pruebas sobre las expresiones OCL para verificar y validar el modelo.

---

## 3. Pruebas de las Condiciones

### ❌ Fallo en la Precondición

Agrega un producto que **ya existe en la venta**, lo que viola la precondición:

```bash
use> !openter venta2 agregarProducto(mouse1, 2)
```

---

### ✅ Cumplimiento de la Precondición

Agrega un producto que **no está en la venta**, cumpliendo con la precondición:

```bash
use> !openter venta2 agregarProducto(teclado1, 2)
```

---

### ❌ Fallo en la Postcondición

Intenta finalizar la operación incorrectamente, por ejemplo, reutilizando un `Item` ya existente (no es un objeto nuevo), lo que rompe la postcondición:

```bash
use> !opexit itemVenta2
```

---

### ✅ Validación Correcta de la Postcondición

Flujo completo correcto que respeta todas las condiciones del modelo:

```bash
-- Paso 1: Inicia la ejecución de la operación agregarProducto en venta2
!openter venta2 agregarProducto(teclado1, 10)

-- Paso 2: Crear o modificar objetos durante la ejecución
!create itemCreadoVenta2 : Item
!set itemCreadoVenta2.cantidad := 10
!insert (teclado1, itemCreadoVenta2) into ProductoItem
!insert (venta2, itemCreadoVenta2) into VentaItem

-- Paso 3: Finaliza la operación
!opexit venta2
```

---

## 4. Expresiones OCL Útiles

```ocl
-- 1. Verificar si una venta contiene un producto específico
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item.producto->
    includes(Producto.allInstances()->any(p | p.codigo = 101))
-- Devuelve true si la venta con fecha "04/06/2025" contiene el producto con código 101.

-- 2. Listar todos los productos de una venta específica
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item.producto
-- Devuelve una colección de productos asociados a los ítems de una venta en esa fecha.

-- 3. Verificar si un producto no está en los ítems de una venta
let v : Venta = Venta.allInstances()->any(v | v.fecha = '04/06/2025') in
let p : Producto = Producto.allInstances()->any(p | p.codigo = 101) in
v.item.producto->excludes(p)
-- Simula la precondición ProductoNoExisteEnVenta.

-- 4. Calcular el total de una venta (suma de subtotales)
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item.subtotal->sum()
-- Suma los subtotales de los ítems de una venta para obtener el total.

-- 5. Validar que todos los ítems tienen correctamente calculado su subtotal
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item->forAll(i |
    i.subtotal = i.cantidad * i.producto.precio
)
-- Verifica la consistencia del modelo como si fuera un test unitario.
```
