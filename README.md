# 9-10-pract

```text
 Вариант 22
 Определить структурный тип, описывающий гостиничный номер (название
 гостиницы, номер, комфортность (люкс, полулюкс стандарт, эконом), количество
 человек, стоимость, бытовые опции). Заполнить структурный массив 20-ю записями.
 Переписать из исходного массива в другой массив, информацию только о тех
 гостиничных номерах, название гостиницы которых оканчивается на сочетание букв
 «hotel». Затем новый массив отсортировать по возрастанию стоимости (рационально
 переставлять все поля структуры разом). Вывести все данные по конкретному
 номеру. Вывести 3 самых дешевых номера и какие бытовые опции у них
 отсутствуют. Реализовать функцию изменения данных конкретного номера. В
 отдельный массив поместить все номера, имеющие балкон. Реализовать вывод
 отфильтрованных данных в виде оберточной функции.
```

```
Решение по требованиям занятия 9:
```
```cpp
#include <iostream>
#include <string>

using namespace std;

enum Comfort // комфорт
{
    LUX,
    SEMI_LUX,
    STANDART,
    ECONOM
};

struct Options // опции
{
    bool conditioner;
    bool fridge;
    bool balcony;
    bool SeaView;
};

struct Room // сам номер
{
    string HotelName;
    int RoomNumber;
    int Persons;
    Comfort comfort;
    Options options;
    double Price;
};

bool hotelEND(string name) // кончается ли на hotel
{
    if (name.length() < 5)
        return false;

    return name.substr(name.length() - 5) == "hotel";
}

int hotelFILTER(Room src[], Room dest[], int size) // берем исх номера, новый массив, размер
{
    int count = 0;
    
    for (int i = 0 ; i < size ; i++)
    {
        if (hotelEND(src[i].HotelName))
        {
            dest[count] = src[i];
            count++;
        }
        
    }
    return count;
}

void sort(Room arr[], int size) // сортируем по ценнику ; пузырьком взял из лекции
{
    for (int a = 0; a < size; a++)
    {
        for (int b = size - 1 ; b > a ; b--)
        {
            if (arr[b-1].Price > arr[b].Price) // сравниваем по цене
            {
                Room temp = arr[b-1];
                arr[b-1] = arr[b];
                arr[b] = temp;
            }
        }
    }
}

string comfortToString(Comfort c) // тк комфорт у нас енум там люкс = 0 и тд и в выводе будет 0 1 2 и тд и делаем так, чтобы оно показывало нормально
{
    switch (c)
    {
        case LUX: return "Люкс";
        case SEMI_LUX: return "Полулюкс";
        case STANDART: return "Стандарт";
        case ECONOM: return "Эконом";
        default: return "Нет такого";
    }
}

void printRoomInf(const Room &r)
{
    cout << "Гостиница:    " << r.HotelName << endl;
    cout << "Номер:        " << r.RoomNumber << endl;
    cout << "Мест:         " << r.Persons << endl;
    cout << "Тип номера:   " << comfortToString(r.comfort) << endl;
    cout << "Цена:         " << r.Price << endl;
    cout << "Кондиционер:  " << (r.options.conditioner ? "есть" : "нет") << endl;
    cout << "Холодильник:  " << (r.options.fridge ? "есть" : "нет") << endl;
    cout << "Балкон:       " << (r.options.balcony ? "есть" : "нет") << endl;
    cout << "Вид на море:  " << (r.options.SeaView ? "есть" : "нет") << endl;
    cout << endl;
}

void PrintRooms(Room arr[], int size) // вывод всего ваще
{
    for (int i = 0; i < size ; i++)
        printRoomInf(arr[i]);
}

void PrintCheapest3(Room arr[], int size)
{
    Room temp[20];

    // копируем массив
    for (int i = 0; i < size; i++)
        temp[i] = arr[i];

    // сортируем по цене
    sort(temp, size);

    cout << "\n3 самых дешевых номера:\n";

    for (int i = 0; i < 3; i++)
    {
        cout << "\nНомер " << i + 1 << " из 3:\n";
        printRoomInf(temp[i]);

        cout << "Отсутствующие опции: ";

        bool first = true;

        if (!temp[i].options.SeaView)
        {
            if (!first) cout << ", ";
            cout << "Вид на море";
            first = false;
        }

        if (!temp[i].options.balcony)
        {
            if (!first) cout << ", ";
            cout << "Балкон";
            first = false;
        }

        if (!temp[i].options.conditioner)
        {
            if (!first) cout << ", ";
            cout << "Кондиционер";
            first = false;
        }

        if (!temp[i].options.fridge)
        {
            if (!first) cout << ", ";
            cout << "Холодильник";
            first = false;
        }

        if (first)
            cout << "нет";

        cout << endl;
        cout << "----------------------------------------" << endl;
    }
}

int FindROOM(Room arr[], int size, int num) // ищем номер для изменения
{
    for (int i = 0 ; i < size ; i ++)
    {
        if (arr[i].RoomNumber == num)
            return i;
    }
    return -1;
}

void UpdateRoom(Room arr[], int size, int number) // меняем ценник у конкретного номера
{
    int index = FindROOM(arr, size, number);
    
    if (index == -1)
    {
        cout << "Нет такого номера" << endl;
        return;
    }
    
    cout << "Введите новую цену: ";
    cin >> arr[index].Price;
    
    cout << "Данные поменяны" << endl;
}

int BalconyFilter(Room src[], Room dest[], int size) // в отдельный массив все что имеет балкон
{
    int count = 0;
    for (int i = 0; i < size; i++)
    {
        if (src[i].options.balcony)
        {
            dest[count] = src[i];
            count++;
        }
    }
    return count;
}

void showBalcony(Room arr[], int size)
{
    Room temp[20];
    int n = BalconyFilter(arr, temp, size);
    cout << "\nС балконом: " << endl;
    PrintRooms(temp,n);
}




int main()
{
    Room rooms[20] =
    {
        {"a hotel", 1, 2, LUX, {true, true, true, true}, 24000},
        {"b hotel", 2, 2, LUX, {true, true, true, true}, 31000},
        {"c", 3, 1, STANDART, {true, true, false, false}, 5220},
        {"d hotel", 4, 3, ECONOM, {true, false, true, true}, 5100},
        {"e", 5, 4, SEMI_LUX, {true, true, true, true}, 9100},
        {"f hotel", 6, 2, LUX, {true, true, true, true}, 12000},
        {"g hotel", 7, 2, STANDART, {true, true, false, false}, 7830},
        {"h", 8, 3, ECONOM, {true, false, true, false}, 1555},
        {"j", 9, 2, STANDART, {true, false, true, false}, 3170},
        {"i", 10, 4, LUX, {true, true, true, true}, 9999},
        {"k", 11, 3, SEMI_LUX, {false, true, true, true}, 6666},
        {"l", 12, 2, STANDART, {true, true, false, false}, 4507},
        {"m", 13, 1, ECONOM, {false, false, false, false}, 2311},
        {"n hotel", 14, 1, LUX, {true, true, true, true}, 12000},
        {"o", 15, 1, SEMI_LUX, {true, true, true, true}, 7999},
        {"p", 16, 4, ECONOM, {true, false, false, true}, 2500},
        {"q hotel", 17, 3, LUX, {true, true, true, true}, 10300},
        {"r", 18, 3, STANDART, {true, true, true, false}, 5200},
        {"s hotel", 19, 2, SEMI_LUX, {true, true, true, false}, 8301},
        {"t", 20, 2, ECONOM, {true, false, false, false}, 3300},
    };
    
    //hotel в названии
    Room filtered[20];
    int n = hotelFILTER(rooms, filtered, 20); // отфильровали
    cout << "\nНомера гостиниц, название которых оканчивается на \"hotel\":" << endl;
    cout << endl;
    PrintRooms(filtered, n);
    
    // сортировка
    sort(filtered, n);
    cout << "\nОтсортированный массив по возрастанию стоимости:" << endl;
    PrintRooms(filtered, n);
    
    
    
    PrintCheapest3(rooms, 20); // 3 самых дешевых
    showBalcony(rooms, 20); // вывод в оберточной функции
    
    
    int num;
    cout << "Вводим номер: " << endl;
    cin >> num;
    
    int idx = FindROOM(rooms, 20, num);

    if (idx != -1)
    {
        cout << "\nДанные по выбранному номеру:\n";
        printRoomInf(rooms[idx]);

        UpdateRoom(rooms, 20, num); // меняем

        cout << "\nОбновленные данные номера:\n";
        printRoomInf(rooms[idx]);
    }
    else
    {
        cout << "нет такого номера" << endl;
    }
    
    
    return 0;
}
```
