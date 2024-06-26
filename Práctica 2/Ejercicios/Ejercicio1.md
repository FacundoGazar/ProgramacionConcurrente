1. Existen N personas que deben ser chequeadas por un detector de metales antes de poder
ingresar al avión.
a. Analice el problema y defina qué procesos, recursos y semáforos serán
necesarios/convenientes, además de las posibles sincronizaciones requeridas para
resolver el problema.

INCISO B

Implemente una solución que modele el acceso de las personas a un detector (es decir, 
si el detector está libre la persona lo puede utilizar; en caso contrario, debe esperar).

```Python
sem mutex=1;
sem dormidos[N]=([N] 0);
bool libre = true;
cola cola(int);

Process Persona [id: 0..N-1]{
	P(mutex)
	if (libre) {
		libre=false;
		V(mutex);
	}
	else {
		cola.push(id);
		V(mutex);
		P(dormidos[id]);
	}
	
	//usarRecurso
	
	P(mutex);
	if (cola.isEmpty) {
		libre= true;
	}
	else {
		sig=cola.pop();
		V(dormidos[sig]);
	}
	V(mutex);
}
```
INCISO C

Modifique su solución para el caso que haya tres detectores. 

```Python
sem mutexProcesos=1;
sem mutexRecursos= 1;
sem dormidos[N]=([N] 0);
int cantDisp = 3;
cola procesos(int);
cola recursos(int);

Process Persona [id: 0..N-1]{
	int recurso, sig;
	
	P(mutexProcesos)
	if (cantDisp !=0) {
		cantDisp--;
		V(mutexProcesos);
	}
	else {
		procesos.push(id);
		V(mutexProcesos);
		P(dormidos[id]);
	}
	
	P(mutexRecursos);
	recurso=recursos.pop();
	V(mutexRecursos);
	//usar el detector de metales de la variable "recurso"
	P(mutexRecursos);
	recursos.push(recurso);
	V(mutexRecursos);
	
	P(mutexProcesos);
	if (procesos.isEmpty) {
		cantDisp++;
	}
	else {
		sig=procesos.pop();
		V(dormidos[sig]);
	}
	V(mutexProcesos);
}
```