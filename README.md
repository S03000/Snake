/*! \file Snake
    \brief Snake
    Snake Game
*/

#include <iostream>
#include <conio.h>
#include<Windows.h>
#include "Time.h"


using namespace std;


bool gameOver; //!<Конец игры
const int width = 20; //!<Ширина поля игры
const int heidht = 20; //!<Высота поля игры
int x,//!<Координата x змейки
y,//!<Координата y змейки 
fruitX,//!<Координата x фрукта
fruitY,//!<Координата y фрукта
score; //!<Очки
int tailx[100],//!<Координаты хвоста x
taily[100]; //!<Координаты хвоста y
int nTail; //!<Длина змейки
/// Движения змейки
enum eDirection {
    STOP = 0,//!<Змейка стоит
    LEFT, //!<Змейка движется влево
    RIGHT, //!<Змейка движется вправо
    UP, //!<Змейка движется вверх
    DOWN //!<Змейка движется вниз
};
eDirection dir; //!<Текущее движение змейки
SYSTEMTIME t;  //!<Текущее время


/** Начальная настройка игры */
/// Производит начальную настройку
void Setup()
{
    gameOver = false;
    dir = STOP;
    x = width / 2 - 1;
    y = heidht / 2 - 1;
    fruitX = rand() % width;
    fruitY = rand() % heidht;
    score = 0;
}

/** Выводит на экран поле, очки и текущее время*/
/// Рисует карту
void Draw()
{
    system("cls");
    GetLocalTime(&t);
    for (int i = 0; i < width + 1; i++)
        cout << "#";
    cout << endl;

    for (int i = 0; i < heidht; i++)
    {
        for (int j = 0; j < width; j++)
        {
            if (j == 0 || j == width - 1)
                cout << "#";
            if (i == y && j == x)
                cout << "0";
            else if (i == fruitY && j == fruitX)
                cout << "F";
            else
            {
                bool print = false;
                for (int k = 0; k < nTail; k++)
                {
                    if (tailx[k] == j && taily[k] == i)
                    {
                        print = true;
                        cout << "o";
                    }
                }
                if (!print)
                    cout << " ";
            }
        }
        cout << endl;
    }

    for (int i = 0; i < width + 1; i++)
        cout << "#";
    cout << endl;
    cout << "Score: " << score << endl;
    Time objTime(t.wHour, t.wMinute, t.wSecond); // объявление объекта и инициализвция элементов данных
    objTime.getTime(); // отобразить время
}

/**Отслежиет, что нажал пользователь */
/// Отслеживает ввод
void Input()
{
    if (_kbhit())
    {
        switch (_getch())
        {
        case 'a':
            dir = LEFT;
            break;
        case 'd':
            dir = RIGHT;
            break;
        case 'w':
            dir = UP;
            break;
        case 's':
            dir = DOWN;
            break;
        case 'x':
            gameOver = true;
            break;
        }
    }
}

/** Описывает длину змейки, координаты змейки и её хвоста, координаты фрукта, считает очки*/
/// Логика игры
void Logic()
{
    int prevX = tailx[0];
    int prevY = taily[0];
    int prev2x, prev2y;
    tailx[0] = x;
    taily[0] = y;
    for (int i = 1; i < nTail; i++)
    {
        prev2x = tailx[i];
        prev2y = taily[i];
        tailx[i] = prevX;
        taily[i] = prevY;
        prevX = prev2x;
        prevY = prev2y;
    }
    switch (dir)
    {
    case LEFT:
        x--;
        break;
    case RIGHT:
        x++;
        break;
    case UP:
        y--;
        break;
    case DOWN:
        y++;
        break;
    }

    //if (x > width || x < 0||y > heidht|| y<0)
    //    gameOver = true;
    if (x >= width - 1)
        x = 0;
    else if (x < 0)
        x = width - 2;
    if (y >= heidht)
        y = 0;
    else if (y < 0)
        y = heidht - 1;

    for (int i = 0; i < nTail; i++)
    {
        if (tailx[i] == x && taily[i] == y)
            gameOver = true;
    }
    if (x == fruitX && y == fruitY)
    {
        score += 10;
        fruitX = rand() % width;
        fruitY = rand() % heidht;
        nTail++;
    }
}

/** Вывод на экран game over при проигрыше*/
/// Конец игры
void End()
{
    cout << "  ###  ##   # #    ###" << endl;
    cout << " #    #  # # # #  #   " << endl;
    cout << " # ## #  # #   #  ### " << endl;
    cout << " #  # #### #   #  #   " << endl;
    cout << "  ##  #  # #   #   ###" << endl;
    cout << "                      " << endl;
    cout << "  ##  #   #  ###  ##  " << endl;
    cout << " #  # #   # #    #  # " << endl;
    cout << " #  # #   # ###  ###  " << endl;
    cout << " #  #  # #  #    #  # " << endl;
    cout << "  ##    #    ### #  # " << endl;
    cout << "Score:"<<score << endl;
}
int main()
{
    Setup();
    while (!gameOver)
    {
        Draw();
        Input();
        Logic();
        Sleep(200);
    }
    system("cls");
    End();
    return 0;
}

Текст модуля Time

#pragma once
#include <iostream>
using namespace std;

/*! Данный класс получает и выводит текущее время на экран */
///Время

class Time
{
private: // спецификатор доступа private
    int hour; //!< час
    int minute; //!< минута
    int second; //!< секунда
public: // спецификатор доступа public

    /** Вызывает функцию установки времени*/
    /// Функция установки времени
    Time(int date_hour, //!< инициализация часа
        int date_minute, //!< инициализация минуты
        int date_second) //!< инициализация секунды
        // конструктор класса
    {
        setTime(date_hour, date_minute, date_second); // вызов функции установки времени
    }


    /** Установка времени в формате чч:мм:сс*/
    /// Устанавливает текущее время

    void setTime(int date_hour,//!< инициализация часа
        int date_minute,//!< инициализация минуты
        int date_second //!< инициализация секунды
    ) // установка времени в формате чч.мм.сс
    {
        hour = date_hour;
        minute = date_minute;
        second = date_second;
    }
    /** Вывод на экран времени в формате чч:мм:сс*/
    /// Выводит текущее время
    void getTime() // отобразить текущее время
    {
        cout << "time: " << hour << ":" << minute << ":" << second << endl;
    }
};

