# tpfinalcom1
tp final comision 1
using System;
using System.Collections.Generic;
using System.Numerics;
using tp1;

namespace tpfinal
{

   public class Estrategia
    {
       private string imprimir(Proceso p)
		{
			return "Nombre: "+ p.nombre +" Tiempo: "+ p.tiempo +" Prioridad: "+ p.prioridad +'\n';
		}
       
        public String Consulta1(List<Proceso> datos)
        {
            string result = " ";
            // Se construye una MinHeap para obtener las hojas
            var heap = new List<Proceso>();
            heap.AddRange(datos);
    
            // Aplicar buildHeap a cada padre para conseguir una Heap
            for (int i = heap.Count / 2 - 1; i >= 0; i--)
            buildHeap(/*i,*/ heap, false);  // false para MinHeap (SJF)
    
          // Las hojas son los nodos a partir de heap.Count / 2
           for (int i = heap.Count / 2; i < heap.Count; i++)
           {
              result += imprimir(heap[i]);  // Imprimir los nodos hoja
           }
           
            return result;

        }



        public String Consulta2(List<Proceso> datos)
        {
            // La altura de una heap completa es log2(número de elementos)
             int altura = (int)Math.Log(datos.Count, 2);
    
             return "Altura de la Heap: " + altura;
        }



        public String Consulta3(List<Proceso> datos)
        {
           string result = "Nivel: 1\n";
           int nivel = 2;
           int nodosPorNivel = 1;
           int nodosImpresos = 0;
    
           // Se construye una MinHeap
           var heap = new List<Proceso>();
           heap.AddRange(datos);
    
          // Aplicar buildHeap a cada padre para conseguir una MinHeap
           for (int i = heap.Count / 2 - 1; i >= 0; i--)
           buildHeap(/*i,*/ heap, false);  // false para MinHeap
    
          // Imprimir los nodos nivel por nivel
           for (int i = 0; i < heap.Count; i++)
            {
                if (nodosImpresos == nodosPorNivel)
            {
               result += "Nivel: " + nivel + '\n';
               nivel++;
            
               nodosPorNivel *= 2;
               nodosImpresos = 0;
             }
        
             result += "- " + imprimir(heap[i]);
             nodosImpresos++;
            }
           
            return result;
        }


        public void ShortesJobFirst(List<Proceso> datos, List<Proceso> collected)
        {

            // Limpiar la salida
            collected.Clear();
    
            // Estructura de datos auxiliar: MinHeap
            var minHeap = new List<Proceso>();
            minHeap.AddRange(datos);
    
            // Aplicar buildHeap a cada padre para conseguir una MinHeap
            for (int i = minHeap.Count / 2 - 1; i >= 0; i--)
            buildHeap(/*i,*/ minHeap, false);  // false indica que es MinHeap (SJF)
    
             // Extraer procesos en orden de menor tiempo
            for (int i = minHeap.Count; i > 0; i--)
            collected.Add(extraer(minHeap, false));  // false indica que es MinHeap
            
        }


        public void PreemptivePriority(List<Proceso> datos, List<Proceso> collected)
        {
           
        	 // Limpiar la salida
             collected.Clear();
    
            // Estructura de datos auxiliar: MaxHeap
             var maxHeap = new List<Proceso>();
             maxHeap.AddRange(datos);
    
            // Aplicar buildHeap a cada padre para conseguir una MaxHeap
            for (int i = maxHeap.Count / 2 - 1; i >= 0; i--)
            buildHeap(/*i,*/ maxHeap, true);  // true indica que es MaxHeap (PPCSA)
    
            // Extraer procesos en orden de mayor prioridad
            for (int i = maxHeap.Count; i > 0; i--)
            collected.Add(extraer(maxHeap, true));  // true indica que es MaxHeap
        	
        }
	 private void buildHeap(List<Proceso> datos, bool isMax) // PARTE ITERATIVA CON COLA
             {
               Queue<int> cola = new Queue<int>();

             // Agregar todos los nodos padre a la cola
             for (int i = datos.Count / 2 - 1; i >= 0; i--)
             {
               cola.Enqueue(i);
              }

           // Procesar cada nodo en la cola
              while (cola.Count > 0)
              {
                int main = cola.Dequeue();  // Nodo principal (raíz de este subárbol)

               int izquierdo = 2 * main + 1;  // Hijo izquierdo
               int derecho = 2 * main + 2;    // Hijo derecho
               int nuevoMain = main;

              // Comparar con el hijo izquierdo
               if ((isMax && izquierdo < datos.Count && datos[izquierdo].prioridad > datos[nuevoMain].prioridad) ||
                 (!isMax && izquierdo < datos.Count && datos[izquierdo].tiempo < datos[nuevoMain].tiempo))
                {
                 nuevoMain = izquierdo;  // Si el hijo izquierdo es mayor (MaxHeap) o menor (MinHeap), hacer que sea la nueva raíz.
                }

                // Comparar con el hijo derecho
                if ((isMax && derecho < datos.Count && datos[derecho].prioridad > datos[nuevoMain].prioridad) ||
                    (!isMax && derecho < datos.Count && datos[derecho].tiempo < datos[nuevoMain].tiempo))
                 {
                    nuevoMain = derecho;  // Si el hijo derecho es mayor o menor, ajustamos la raíz.
                 }

                // Si no hay cambios, continuar con el siguiente nodo
                if (nuevoMain != main)
                 {
                   // Si hay cambios, intercambiar y agregar el nuevoMain a la cola
                    swap(main, nuevoMain, datos);
                    cola.Enqueue(nuevoMain); // Agregar el nodo que fue movido a la cola
                 }
               }
              }
 //Este método extrae la raíz del heap, ya sea el proceso con menor tiempo (en SJF) o el de mayor prioridad (en PPCSA), 
            //y reorganiza el heap para mantener su estructura.
            
            private Proceso extraer(List<Proceso> datos, bool isMax)
               {
               // Guardar el valor de la raíz (el proceso a retornar)
                var aRetornar = datos[0];
    
              // Mover el último elemento a la raíz y eliminar el último nodo
                datos[0] = datos[datos.Count - 1];
                datos.RemoveAt(datos.Count - 1);
    
              // Reorganizar el heap
                buildHeap(/*0,*/ datos, isMax);
    
              // Retornar el proceso extraído
               return aRetornar;
                }

            //Este método simplemente intercambia dos elementos en la lista.
            private void swap(int a, int b, List<Proceso> lista)
              {
                Proceso temp = lista[a];
                lista[a] = lista[b];
                lista[b] = temp;
              }



    }
}

