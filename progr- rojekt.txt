#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
char *wejscie;//globalne zdefiniowanie miejsca gdzie przechowuje dzialanie

double charnaf(double i1, double i2) // funkcja odpowiadajac danych typu char na double
{
    int n, i;
    double liczba = 0;

    for(i = i1; i <= i2; i++) {
        n = wejscie[i] - '0';

        if (n < 0 || n > 9) {
            return 0;
        }
        liczba = 10 * liczba + n;
    }

    return liczba;
}

int operacja (int op) // funkcja do sprawdzenia czy dany znak jest operacja czy nie
{
    switch (op) {
    case '+':   return 1;
    case '-':   return 1;
    case '*':   return 2;
    case '/':   return 2;
    }

    return 0;
}

double calc(int op, double a, double b)
{
    switch (op) {
    case '+':   return a + b;
    case '-':   return a - b;
    case '*':   return a * b;
    case '/':   if (b == 0) {
                    printf("Nie mozna dzielic przez 0\n");
                    exit(1);
                }
                return a / b;
    }

    return 0;
}

double licz(int poczatek, int koniec) // ogolna analiza wprowadzonego dzialania
{
    int prwn = 0;
    int mid = -1;
    int i;

    if (poczatek > koniec) {
        return 0;
    }
    for(i = poczatek; i <= koniec; i++) {
        int c = wejscie[i];

        if (c == '(') {
            prwn++;
        } else if (c == ')') {
            if (prwn == 0) {
                printf("Brakuje otwarcia nawiasu\n");
                exit(0);
            }
            prwn--;
        } else if (prwn == 0) {
            int n = operacja(c); // jesli c to nie operacja to n=0, jesli c to - lub + to n=1, jesli c to * lub / to n=2

            if (n && (mid < 0 || n < operacja(wejscie[mid]))) {
                mid = i;
            }
        }
    }

    if (prwn > 0) {
        printf("Brakuje zamkniecia nawiasu\n");
        exit(0);
    }

    if (mid >= 0) { // wyzej ustalilismy miejsca operandow w dzialaniu i teraz chcac wykonac dzialanie chcemy je tak jakby pominac zeby zostaly tymi operandami
            double a = licz(poczatek, mid - 1);
            double b = licz(mid + 1, koniec);
            double res =  calc(wejscie[mid], a, b);//wejscie[mid] to operand
            printf("%.1f %c %.1f = %.1f\n", a, wejscie[mid], b, res);
            return res;
    } else {
        if (wejscie[poczatek] == '(' && wejscie[koniec] == ')'){// dodane bo po wpisaniu calego dzialania w nawias kod sie zle wykonwyal
            return licz(poczatek + 1, koniec - 1);
        }
        double res = charnaf(poczatek, koniec); // zamiana wyniku z char na double
        return res;
    }

    return 0;
}

int main(){
    wejscie = malloc(sizeof(char) * 10000);
    char r;
    int i = 0;
    double wynik;
    printf("Wpisz dzialanie (Uwaga liczby ujemne zapisuj w nawiasie!)\n");
    scanf("%c", &r); // tutaj skan pierwszego znaku / while a nie for bo nie znamy dlugosci dzialania i dlatego tez %c a nie %s
    while(r != '\n'){ //np 2+3*2 wejscie[0]=2 zwiekszamy i o 1 i do r skanujemy nastpny znak i przypisujemy go do wejscie[1] itd. dopko nast znak to nie enter
        if(r != ' '){
            wejscie[i] = r;
            i++;
        }
        scanf("%c", &r);
    }
    wynik = licz(0,i-1);//max liczba znakow ktora moze przechowywac bufor to o 1 mniejsza niz rozmiar tablicy dlatego i-1
    printf("Wynik to: %.1f\n", wynik);
    return 0;
}
