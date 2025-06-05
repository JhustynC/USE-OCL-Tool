# Demo de la herramienta USE: UML-based Specification Environment
#### Realizado por: Jhustyn Carvajal
#### Fecha: 06/06/2025

## 1. Contenido
- use-6.0.0.zip: Instalador, solo descomprimir en C:// y ejecutar el bin/use.bat
- model.use: Definicion del modelo UML en lenguaje use
- usage.scenario.soil: Script para la crecion de instancias del model

## 2. Pasos para levantar el proyecto
- a. Ejecutar USE
```bash
linux: ./use
windows: ./use.bat
```
- b. Cargar el model.use desde la interfaz grafica (Open Specification)
- c. Mediante la consola que proporciona el porgrama, ejecutar el archivo usage.scenario.soil: 
```bash 
use> open ../usage.scenario.soil
```
- d. Realizar pruebas sobre el OCL para verificar y validar el modelo 


## 3. Pruebas de las Condiciones

```bash
# Hacer fallar la **precondición**
# Agrega un producto que **ya existe en la venta**, lo que viola la condición definida en:
use> !openter venta2 agregarProducto(mouse1, 2)

```
```bash
# Cumplir la precondición
# Agrega un producto que no está en la venta, cumpliendo con la precondición.
use> !openter venta2 agregarProducto(teclado1, 2)

```
```bash
# Hacer fallar la postcondición
# Intenta finalizar la operación de forma incorrecta, por ejemplo, devolviendo un Item que ya existía (no es un objeto nuevo). Esto rompe la siguiente postcondición:
use> !opexit itemVenta2

```
```bash
# Validar correctamente la postcondición
# Este es un flujo completo y correcto, que respeta las condiciones del modelo:
-- 1. Inicia ejecución de la operación agregarProducto en venta2
!openter venta2 agregarProducto(teclado1, 10)

-- 2. Durante la ejecución, se crean o modifican objetos como Item
-- Si estás haciendo esto manualmente:
!create itemCreadoVenta2 : Item
!set itemCreadoVenta2.cantidad := 10
!insert (teclado1, itemCreadoVenta2) into ProductoItem
!insert (venta2, itemCreadoVenta2) into VentaItem

-- 3. Finaliza la operación
!opexit venta2

```


## 4. Algunas expresiones OCL

```js
# 1. Verificar si una venta contiene un producto específico
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item.producto->includes(Producto.allInstances()->any(p | p.codigo = 101))
# Esto devuelve true si la venta con fecha "04/06/2025" contiene el producto con código 101.

# 2. Listar todos los productos de una venta específica
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item.producto
# Esto muestra una colección de productos relacionados a los ítems de una venta con esa fecha.

# 3. Verificar si un producto no está en los ítems de una venta (como en el pre)
let v : Venta = Venta.allInstances()->any(v | v.fecha = '04/06/2025') in
let p : Producto = Producto.allInstances()->any(p | p.codigo = 101) in
v.item.producto->excludes(p)
# Esto simula la condición del precondicional ProductoNoExisteEnVenta.

# 4. Evaluar el total de una venta (suma de subtotales)\
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item.subtotal->sum()
# Esto calcula el total de la venta sumando los subtotales de los ítems.

# 5. Ver si todos los ítems de una venta tienen su subtotal bien calculado (cantidad * precio)
Venta.allInstances()->any(v | v.fecha = '04/06/2025').item->forAll(i |
    i.subtotal = i.cantidad * i.producto.precio
)
# Este es un buen test de consistencia del modelo (Simula un "assert" de prueba unitaria).

```