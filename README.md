# semana10-Mejoramiento del sistema de gestión de inventarios realizado la anterior semana para un local de venta de productos quimicos y envases para que utilice archivos para almacenar y recuperar la información del inventario y maneje excepciones durante la lectura y escritura de archivos.

import os

class Producto:
    def __init__(self, nombre, tipo, cantidad, precio):
        self.nombre = nombre
        self.tipo = tipo  # "químico" o "envase"
        self.cantidad = cantidad
        self.precio = precio

    def __repr__(self):
        return f"{self.nombre},{self.tipo},{self.cantidad},{self.precio}"


class Inventario:
    def __init__(self, archivo='inventario.txt'):
        self.archivo = archivo
        self.productos = self.cargar_inventario()

    def cargar_inventario(self):
        productos = {}
        try:
            if os.path.exists(self.archivo):
                with open(self.archivo, 'r') as f:
                    for linea in f:
                        nombre, tipo, cantidad, precio = linea.strip().split(',')
                        productos[nombre] = {
                            'tipo': tipo,
                            'cantidad': int(cantidad),
                            'precio': float(precio)
                        }
        except FileNotFoundError:
            print(f"Archivo {self.archivo} no encontrado. Creando un nuevo archivo.")
        except PermissionError:
            print(f"Permiso denegado para leer el archivo {self.archivo}.")
        except Exception as e:
            print(f"Error inesperado al cargar el inventario: {e}")
        return productos

    def guardar_inventario(self):
        try:
            with open(self.archivo, 'w') as f:
                for nombre, datos en self.productos.items():
                    f.write(f"{nombre},{datos['tipo']},{datos['cantidad']},{datos['precio']}\n")
            print("Inventario guardado exitosamente.")
        except PermissionError:
            print(f"Permiso denegado para escribir en el archivo {self.archivo}.")
        except Exception as e:
            print(f"Error inesperado al guardar el inventario: {e}")

    def agregar_producto(self, producto):
        if producto.nombre in self.productos:
            self.productos[producto.nombre]['cantidad'] += producto.cantidad
        else:
            self.productos[producto.nombre] = {
                'tipo': producto.tipo,
                'cantidad': producto.cantidad,
                'precio': producto.precio
            }
        self.guardar_inventario()

    def eliminar_producto(self, nombre, cantidad):
        if nombre in self.productos:
            if self.productos[nombre]['cantidad'] >= cantidad:
                self.productos[nombre]['cantidad'] -= cantidad
                if self.productos[nombre]['cantidad'] == 0:
                    del self.productos[nombre]
                print(f"Producto {nombre} eliminado del inventario.")
            else:
                print(f"No hay suficiente {nombre} en el inventario para eliminar {cantidad} unidades.")
        else:
            print(f"El producto {nombre} no existe en el inventario.")
        self.guardar_inventario()

    def mostrar_inventario(self):
        if self.productos:
            for nombre, datos en self.productos.items():
                print(f"Nombre: {nombre}, Tipo: {datos['tipo']}, Cantidad: {datos['cantidad']}, Precio: {datos['precio']}")
        else:
            print("El inventario está vacío.")


def menu():
    inventario = Inventario()

    while True:
        print("\n1. Agregar producto")
        print("2. Eliminar producto")
        print("3. Mostrar inventario")
        print("4. Salir")
        opcion = input("Seleccione una opción: ")

        if opcion == '1':
            nombre = input("Nombre del producto: ")
            tipo = input("Tipo (químico/envase): ")
            cantidad = int(input("Cantidad: "))
            precio = float(input("Precio: "))
            producto = Producto(nombre, tipo, cantidad, precio)
            inventario.agregar_producto(producto)

        elif opcion == '2':
            nombre = input("Nombre del producto: ")
            cantidad = int(input("Cantidad a eliminar: "))
            inventario.eliminar_producto(nombre, cantidad)

        elif opcion == '3':
            inventario.mostrar_inventario()

        elif opcion == '4':
            print("Saliendo del sistema de gestión de inventarios.")
            break

        else:
            print("Opción no válida. Intente de nuevo.")

if __name__ == "__main__":
    menu()
    
