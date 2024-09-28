#include <stdio.h>
#include <math.h>
#include <unistd.h>
#include <time.h>

#define HEIGHT 50
#define WIDTH 50

// Inizializzazione della matrice con spazi vuoti
void InMatrix(char matrix[HEIGHT][WIDTH]);
// Disegna il cuore sulla matrice utilizzando l'equazione matematica.
void draw(char matrix[HEIGHT][WIDTH],double ,double ,int ,int ,double);
// Serve per visualizzarlo sul schermo
void printMatrix(char matrix[HEIGHT][WIDTH]);
// Cancella la console
void clearScreen();

int main() 
{
    char matrix [HEIGHT] [WIDTH];

    // è una conversione di coordinate matrice in coordinate cartesiane
    // Le Variabili "d*" servono per gestire la coordinata in formato double per una maggiore precisione.
    double dy = 0;
    double dx = 0;
    const int centerX = WIDTH / 2;
    const int centerY = HEIGHT / 2;
    int vScala [] = {4, 5, 6, 7, 8}; // Per creare un effetto di pulsazione è stato creato un array che modifica la dimensione del cuore
    int nScala = 0; // valore iniziale dell'array vScala
    int dimMin = 0;
    int dimMax = 4;
    int incr = 1;

    int durata = 4; // durata secondi dell'animazione

    /* 
        Loop per la pulsazione del cuore
        Per uscire dal ciclo devi fare un control + C
    */
    int flag = 1;
    while (flag) { // Loop infinito per disegnare il cuore in continuazione
        clearScreen();

        double scala = WIDTH / vScala [nScala];

        InMatrix(matrix);
        draw(matrix, dx, dy, centerX, centerY, scala);
        printMatrix(matrix);

        // Calcola l'intervallo dinamicamente
        int interval = durata * 400000 / vScala[nScala];

        struct timespec ts;
        ts.tv_sec = interval / 1000000;
        ts.tv_nsec = (interval % 1000000) * 1000;
        nanosleep(&ts, NULL);



        nScala = (nScala + incr);
        if(nScala == dimMax)
        {
            incr = -1;
        }
        else if (nScala == dimMin)
        {
            incr = 1;
        }

    }

    return 0;
}

void InMatrix(char matrix[HEIGHT][WIDTH]) 
{
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            matrix[i][j] = ' ';
        }
    }
}

void draw(char matrix[HEIGHT][WIDTH], double dx, double dy, int centerX, int centerY, double scala) 
{
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            // Calcola le coordinate normalizzate rispetto al centro della matrice e alla scala.
            dx = (double) ( j - centerX) / scala;
            dy = -(double) ( i - centerY) / scala;
            
            // Verifica se il punto soddisfa l'equazione del cuore.
            // Il 0.4 è una aggiunta all'equazione matematica per arrontondare il cuore
            if (pow(0.4 * dx * dx + dy * dy - 1, 3) - dx * dx * dy * dy * dy <= 0.0) {
                matrix[i][j] = '*';
            } else {
                matrix[i][j] = ' ';
            }
        }
    }
}

void printMatrix(char matrix[HEIGHT][WIDTH]) {
    printf("\033[31m"); // Imposta il colore rosso

    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            printf("%c", matrix[i][j]);
        }
        printf("\n");
    }

    printf("\033[0m"); // Ripristina il colore originale
}

void clearScreen()
{
    /*
        Tre metodi per cancellare lo schermo sono stati inclusi per garantire la compatibilità tra diversi sistemi operativi. 
        Provate i metodi precedenti se il terzo non funziona.

        test 1 e 2 utilizzano il codice ASCII per cancellare il terminale
    */


    // Test 1
    //const char *CLEAR_SCREEN_ANSI = "\e[1;1H\e[2J";
    //write(STDOUT_FILENO, CLEAR_SCREEN_ANSI, 12);

    // Test 2
    //printf("\033[2J\033[H");
    //fflush(stdout);

    // Test 3 
    // Funzionante su Windows e Linux
    #ifdef _WIN32
        system("cls");
    #else
        system("clear");
    #endif
}


//made by CodeArtist06 (GitHub)