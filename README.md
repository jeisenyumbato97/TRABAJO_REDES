"""Libreria que me sirve para crear la tabla de los vertices y distancias 
Para que funcione colocar -> pip install texttable"""
from texttable import Texttable

#Clase para definir un grafo
class Grafo:
    def __init__(self):
        self.vertices = {}
    
    #Metodo para añadir vertice
    def add_vertice(self, nro_vertice):
        nodo = Vertice(nro_vertice)
        self.vertices[nro_vertice] = nodo
 
    #Metodo para obtener vertice
    def get_vertice(self, nro_vertice):
        return self.vertices[nro_vertice]

    #Metodo para añadir arista, recibe los dos vertices que van a ser conectados por una arista
    def add_arista(self, src_nro_vertice, dest_nro_vertice, peso=1):
        self.vertices[src_nro_vertice].add_vertice_adyacente(self.vertices[dest_nro_vertice], peso)
 
    #Metodo para obtener la longitud del diccionario de vertices
    def __len__(self):
        return len(self.vertices)
 
    #Metodo para obtener los valores del diccionario de vertices
    def __iter__(self):
        return iter(self.vertices.values())
        
    #Metodo para saber si la arista existe
    def arista_existe(self, src_nro_vertice, dest_nro_vertice):
        return self.vertices[src_nro_vertice].es_adyacente(self.vertices[dest_nro_vertice])
 
#Clase para definir un vertice
class Vertice:
    #Definiendo metodo constructor
    def __init__(self, nro_vertice):
        self.nro_vertice = nro_vertice
        self.vertices_adyacentes = {}
 
    #Metodo para obtener llave para el diccionario vertice
    def get_key(self):
        return self.nro_vertice
 
    #Metodo para añadir vertice adyacente 
    def add_vertice_adyacente(self, dest_nro_vertice, peso):
        self.vertices_adyacentes[dest_nro_vertice] = peso
 
    #Metodo para obtener las llaves del diccionario vertices adyacentes
    def get_vertices_adyacentes(self):
        return self.vertices_adyacentes.keys()
 
    #Metodo para obtener el peso
    def get_peso(self, dest_nro_vertice):
        return self.vertices_adyacentes[dest_nro_vertice]
    
    #Metodo para saber si es un vertice adyacente
    def es_adyacente(self, destino):
        return destino in self.vertices_adyacentes
 

def bellman_ford(grafo, vertice_origen):
    """Establecemos con dict.fromkeys que el valor que se establece en cada elemento
    del diccionario será infinito"""
    distancia = dict.fromkeys(grafo, float('inf'))
    #Asignar al vertice origen una distancia = 0
    distancia[vertice_origen] = 0

    #Como no voy a usar el i -> (for i in range()), se coloca un _ 
    for _ in range(len(grafo) - 1):
        for vertice in grafo:
            for vertice_adyacente in vertice.get_vertices_adyacentes():
                distancia[vertice_adyacente] = min(distancia[vertice_adyacente], distancia[vertice] + vertice.get_peso(vertice_adyacente))
                
    return distancia
    
    
def crearGrafo():
    #Inicializar la clase grafo
    g = Grafo()

    while True:
        #Mostrar en pantalla al usuario, el split hará que el usuario_input se vuelva un arreglo
        usuario_input = input('Desea Agregar vertice/Agregar arista/Bellman-Ford/Mostrar/Salir: ').split()
     
        #Se usa lower para que no importe si el usuario escribe en minuscula o mayuscula
        operation = usuario_input[0].lower()
        #Si el usuario coloca agregar -> se agrega un vertice
        if operation == "agregar":
            suboperation = usuario_input[1]
            #Verificar si el vertice pertenece al grafo
            if suboperation == 'vertice':
                key = int(usuario_input[2])
                if key not in g:
                    g.add_vertice(key)
                else:
                    print('El vertice ya existe.')
            #Si el usuario coloca arista -> se agrega una arista
            elif suboperation == 'arista':
                #Vertice inicial
                src = int(usuario_input[2])
                #Vertice destino
                dest = int(usuario_input[3])
                #Peso de la arista
                weight = int(usuario_input[4])
                #Verificar si los vertices pertenece al grafo
                if src not in g.vertices:
                    print('El nodo {} no existe'.format(src))
                elif dest not in g.vertices:
                    print('El nodo {} no existe.'.format(dest))
                else:
                    #Verificar si la arista existe
                    if not g.arista_existe(src, dest):
                        g.add_arista(src, dest, weight)
                    else:
                        print('La arista ya existe.')
     
        #Si el usuario coloca bellman-ford
        elif operation == 'bellman-ford':
            key = int(usuario_input[1])
            vertice_origen = g.get_vertice(key)
            distancia = bellman_ford(g, vertice_origen)   

            #Ordenar la tabla en el que se mostrará los vertices y las distancias         
            nodos = []
            for nodo in distancia:
                nodos.append((nodo.get_key(), distancia[nodo]))
            nodos =sorted(nodos, key=lambda tup: tup[0])        
            
            #Crear la tabla en el que se mostrará los vertices y las distancias
            t = Texttable()
            row = ["Vertice"]
            row2 = ["Distancia"]
            for nodo in nodos:
                row.append(nodo[0])
                row2.append(nodo[1])
            t.add_row(row)
            t.add_row(row2)
            print(t.draw())
     
        #Mostrar los vertices y las aristas
        elif operation == "mostrar":
            print('Vertices: ')
            for v in g:
                print(v.get_key())
            print()
     
            print('Aristas: ')
            for v in g:
                for dest in v.get_vertices_adyacentes():
                    w = v.get_peso(dest)
                    print('(src={}, dest={}, peso={}) '.format(v.get_key(), dest.get_key(), w))
            print()

        #Salir del grafo
        elif operation == 'salir':
            break
 
def main():
    crearGrafo()   
    

if __name__ == "__main__": main()
