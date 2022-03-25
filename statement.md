#include <omp.h>
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>

int main (int argc, char *argv[]) {
	clock_t Ticks[2];
	Ticks[0] = clock();
	int tid,n, i, threads = 1;//threads
	double  a = 0, b = 100,dis= 0.0000001;//dis  = 0.000001 0.00001 0.0001
	double integral = 0,result=0,x; 
	double myResult[threads];
	
	//Calcula a integral nos limites
	result = sqrt(10000*10000-a*a)/2;
	result += sqrt(abs(10000*10000-b*b))/2;
	n = (b-a)/(dis);
	
// static, dynamic, guided, CHUNK
#define CHUNK 100

#pragma omp parallel for schedule(static) num_threads(threads) shared(myResult)
	for(i = 0; i<threads; i++)myResult[i] = 0;
		
// threads com suas copias das variaveis */	
#pragma omp parallel for schedule(static) num_threads(threads) shared(a,result,dis,n,myResult) private(x,tid)
	for(i = 0;i<=n;i++){//Calcula a integral nos pontos do meio
		// Descobre seu ID
		tid = omp_get_thread_num(); 
		x = a+i*dis;
		myResult[tid] += sqrt(10000*10000-x*x);
		
	}// Todos os threads se juntam ao thread principal e somem
	
	for(i = 0;i<threads;i++){
		result += myResult[i];
	}
	
	//Multiplicar pela divisao
	integral += result*dis;
	printf("Result: %f\n", integral);
	Ticks[1] = clock();
    double Tempo = (Ticks[1] - Ticks[0]) * 1000.0 / CLOCKS_PER_SEC;
    printf("Tempo gasto: %g ms.", Tempo);
	
	return 0;
}