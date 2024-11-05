#include <stdio.h>
#include <stdbool.h>
#include <stdlib.h>
#include <getopt.h>
#include <string.h>
#include <ctype.h>

#include "input.h"

bool eflag = false; // Opción -e, switch to english
bool iflag = false; // Opción -i, ingresa una línea de texto
bool aflag = false; // Opción -a, orden ascendente
bool dflag = false; // Opción -d, orden descendente

void print_help(char *command)
{
	printf("Programa en C ejemplo, imprime argumentos ingresados en consola.\n");
	printf("uso:\n %s [-i] [-e] [-a] [-d] [arg 1] [arg 2] ... [arg n]\n", command);
	printf(" %s -h\n", command);
	printf("Opciones:\n");
	printf(" -h\t\t\tAyuda, muestra este mensaje\n");
	printf(" -e\t\t\tSwitch to english\n");
	printf(" -i\t\t\tIngresa una línea de texto\n");
	printf(" -a\t\t\tOrdena los argumentos en orden ascendente\n");
	printf(" -d\t\t\tOrdena los argumentos en orden descendente\n");
}

// Función para ordenar un vector de palabras en orden ascendente
void sort(char **text, int n) {
	char *temp;
	int i, j;
	for(i = 0; i < n; i++) {
		for (j = i + 1; j < n; j++) {
			if (strcmp(text[i], text[j]) > 0) {
				temp = text[i];
				text[i] = text[j];
				text[j] = temp;
			}
		}
	}
}

// Función para ordenar en orden ascendente usando sort
void ordenar_ascendente(char **args, int num_args) {
	sort(args, num_args);
}

// Función para ordenar en orden descendente usando sort
void ordenar_descendente(char **args, int num_args) {
	sort(args, num_args);
	
	// Invertir el arreglo después de ordenarlo en ascendente para obtener descendente
	for (int i = 0; i < num_args / 2; i++) {
		char *temp = args[i];
		args[i] = args[num_args - i - 1];
		args[num_args - i - 1] = temp;
	}
}

int main(int argc, char **argv)
{
	int opt, index;

	// Este lazo recorre los argumentos buscando las opciones indicadas
	while ((opt = getopt(argc, argv, "iehad")) != -1) {
		switch(opt) {
			case 'i':
				iflag = true;
				break;
			case 'e':
				eflag = true;
				break;
			case 'a':
				aflag = true;
				break;
			case 'd':
				dflag = true;
				break;
			case 'h':
				print_help(argv[0]);
				return 0;
			case '?':
			default:
				fprintf(stderr, "uso: %s [-i] [-e] [-a] [-d] [arg 1] [arg 2] ... [arg n]\n", argv[0]);
				fprintf(stderr, "     %s -h\n", argv[0]);
				return -1;
		}
	}

	// Verifica si ambas opciones de ordenamiento están activas
	if (aflag && dflag) {
		fprintf(stderr, "Error: No se puede usar -a y -d al mismo tiempo\n");
		return -1;
	}

	// Determina el número de argumentos no opción
	int num_args = argc - optind;
	char **args = argv + optind;

	// Llama a la función correspondiente para ordenar
	if (aflag) {
		ordenar_ascendente(args, num_args);
	} else if (dflag) {
		ordenar_descendente(args, num_args);
	}

	// Imprime los argumentos que no son opción
	for (index = optind; index < argc; index++) {
		if (eflag)
			printf("Non-option argument: %s\n", argv[index]);
		else
			printf("Argumento no-opción: %s\n", argv[index]);
	}

	// Ingresa una línea de texto desde consola y la reimprime en consola
	if (iflag) {
		char *texto;
		get_from_console(&texto);
		printf("%s\n", texto);
		free(texto);
	}

	// El programa se invocó sin usar opciones o argumentos
	if (argc == 1)
		print_help(argv[0]);

	return 0;
}
