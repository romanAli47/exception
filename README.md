#include <iostream>
#include <new>
#include <string>
#include <vector>
#include <exception>
#include <algorithm>
#include <conio.h>
#include <ostream>
#include <sstream>
#include <fstream>//для работы с файлами
using namespace std;

int i = 0;
bool toFile(0);//bool принемает два значения: правда или ложь
ifstream f_In;//файловый ввод
ofstream f_Out;//файловый вывод
class Magazin;
typedef vector <Magazin> Base;//Переименование типа - необязательно
vector <Magazin>::iterator index;//Определение итератора index (нужен для прямого доступа к элементам (методы begin() и end()))
Base bs;

class ex1 : public exception
{
public: ex1(char *s) : exception(s) {}
};

class ex2 : public ex1
{
public: ex2(char *s) : ex1(s) {}
};

class Magazin
{
private:
	string Name;
	string Mark;
	char Type;
	int Price;
	int Kolich;
public:
	Magazin()
	{
		Price = 0;
	}
	Magazin(string N, string M, char T, int P, int K)
	{
		Name = N;
		Mark = M;
		Type = T;
		Price = P;
		Kolich = K;
	}

	string GetMark()
	{
		return Mark;
	}

	void InsertVector(Magazin Univer)
	{
		bs.push_back(Univer);//Добавление элементов в конец контейнера 
	}

	void InputRecord() //throw (int) 
	{
		int nBuf = 0;
		string countBuf;

		cout << "Введите название музыкального инструмента: ";
		cin >> Name;
		int nSize = Name.length();
		for (int i = 0; i < nSize; i++){
			if (!isalpha(Name[i]))  throw 1; //isalpha - проверяет аргумент Name[i] является ли он строчной или прописной буквой алфавита
		}

		cout << "Введите марку инструмента: ";
		cin >> Mark;
		nSize = Mark.length();
		for (int i = 0; i < nSize; i++){
			if (!isalpha(Mark[i]) && !isdigit(Mark[i]))  throw 2;
		}

		string sBufType;
		cout << "Введите тип инструмента ('g' - Акустический, 'c' - Электрический): ";
		cin >> sBufType;
		if (sBufType.length() == 1){
			if ((sBufType[0] != 'g') && (sBufType[0] != 'c')) throw 3;
		}
		else throw 3;
		Type = sBufType[0];

		cout << "Введите цену музыкального инструмента: ";
		cin >> countBuf;
		int x = countBuf.length();
		for (int i = 0; i < x; i++){
			if (!isdigit(countBuf[i])){
				throw 4;
			}
			Price = atoi(countBuf.c_str());//Преобразуем countBuf.c_str в целое значение типа int
		}

		cout << "Введите количество на складе (max кол-во 10): ";
		cin >> Kolich;
		if (Kolich < 0) throw ex1(" ***Ошибка!*** Error left");
		if (Kolich > 10) throw ex2(" ***Ошибка!*** Количество инструментовна складе больше допустимго хранения [1;10]");
		while (!(cin >> Kolich) || (Kolich = (int)Kolich) < 1 || (Kolich = (int)Kolich) > 10){
			cout << "Некорректное число!" << endl;
			cin.clear();
			while (cin.get() != '\n');
		}
	}

	void SortByMark()//Сортировка по названию университета
	{
		if (bs.size() != 0)
			sort(bs.begin(), bs.end());/*Сортировка в лексикографическом порядке,
									   Метод begin возвращает значение итератора – указателя на начало контейнера, метод end – на конец*/
	}

	void ShowInfo(bool toFile)
	{
		if (toFile){
			cout << "Название инструмента: " << Name << endl;
			f_Out << "Название инструмента: " << Name << endl;
			cout << "Марка инструмента: " << Mark << endl;
			f_Out << "Марка инструмента: " << Mark << endl;
			if (Type == 'c'){
				cout << "Тип инструмента: электрический " << endl;
				f_Out << "Тип инструмента: электрический " << endl;
			}
			else{
				cout << "Тип инструмента: акустический " << endl;
				f_Out << "Тип инструмента: акустический " << endl;
			}
			cout << "Цена музыкального инструмента: " << Price << endl;
			f_Out << "Цена музыкального инструмента: " << Price << endl;
			cout << "Количество на складе: " << Kolich << endl;
			f_Out << "Количество на складе: " << Kolich << endl;
		}

		else{
			cout << "Название инструмента: " << Name << endl;
			cout << "Название инструмента: " << Mark << endl;
			if (Type == 'c'){
				cout << "Тип инструмента: электрический " << endl;
			}
			else{
				cout << "Тип инструмента: акустический " << endl;
			}
			cout << "Цена музыкального инструмента: " << Price << endl;
			cout << "Количество на складе: " << Kolich << endl;
		}
	}

	void ShowVector()//Вывод хранилища на экран
	{
		int n = bs.size();
		if (n != 0){
			for (int j = 0; j < n; j++){
				cout << endl;
				bs[j].ShowInfo(toFile);
				cout << endl;
			}
		}
		else cout << endl << "Пусто!";
	}

	void SearchName(string SrchName)//Поиск записи по фамилии студента
	{
		toFile = true;
		int k = bs.size();
		bool isFinded = false;
		for (int l = 0; l < k; l++){
			if (SrchName == bs[l].Name){
				bs[l].ShowInfo(true);
				isFinded = true;
			}
		}

		if (!isFinded){//Не нашли ну и ладно
			cout << "Ничего не найдено!" << endl;
			getch();
		}
		toFile = false;
	}

	void ReadFromFile()//Функция извлечения информации из файла
	{
		string bufName;
		string bufMark;
		char bufType;
		int bufPrice;
		int bufKolich;

		if (f_In.is_open()){
			while (!f_In.eof()){
				f_In >> bufName >> bufMark >> bufType >> bufPrice >> bufKolich;
				bs.push_back(Magazin(bufName, bufMark, bufType, bufPrice, bufKolich));//Добавление элементов в конец контейнера 
			}
			cout << "Всё хорошо";
			getch();
		}
		else{
			cout << "Входной файл не открыт!";
			getch();
		}
	}

	void ClearVector()//Функция очистки рабочей станции
	{
		int nVecSize = bs.size();
		if (nVecSize == 0){
			cout << endl << "Уже пусто!";
		}
		else bs.clear();
	}

	Magazin& operator [] (int i) //Перегрузка индексации для Magazin
	{
		return bs[i];
	}

	bool operator < (Magazin Univers)//Оператор сравнения (для сортировки объекта)
	{
		if (Name < Univers.GetMark()){
			return true;
		}
		else return false;
	}

	void *operator new(size_t size);

	void operator delete(void *p);

	~Magazin() {}
};

void *Magazin::operator new(size_t size)
{
	void *p;
	cout << "Выделение памяти для объекта класса Magazin\n";
	p = malloc(size);
	try
	{
		if (!p){
			bad_alloc ba;
			throw ba;
		}
	}
	catch (bad_alloc ba)
	{
		cout << "Ошибка при выделении памяти\n";
	}
}

void Magazin::operator delete(void *p)
{
	cout << "Удаление объекта класса Magazin\n";
	free(p);
}

void PrintMenu()//Меню
{
	system("cls");
	cout << "         ********** Главное меню **********" << endl;
	cout << "Нажмите '1' для заполнения хранилища с клавиатуры." << endl;
	cout << "Нажмите '2' для загрузки информации из файла на рабочую станцию." << endl;
	cout << "Нажмите '3' для вывода хранилища на экран." << endl;
	cout << "Нажмите '4' для нахождения информации по названию инструмента." << endl;
	cout << "Нажмите '5' для сортировки хрнилища по марке инструмента." << endl;
	cout << "Нажмите '6' для очистки рабочей станции." << endl;
	cout << "Нажмите '7' для ВЫХОДА." << endl << endl;
	cout << "Ваш выбор: ";
}

int main()
{
	setlocale(LC_ALL, "rus");
	try
	{
		f_In.open("D:\\in.txt");
		f_Out.open("D:\\out.txt", ios::in | ios::out);
		if (!f_In || !f_Out) //Если файл не найден
			throw 2.2;
	}
	catch (double)
	{
		cout << endl << "***Ошибка!Файл не найден***" << endl;
		getch();
	}

	Magazin Univers;
	char iMenuChoice(0);
	string srchName;

	while (iMenuChoice != '7'){
		PrintMenu();
		cin >> iMenuChoice;

		switch (iMenuChoice)
		{
		case '1':
			while (i < 3){
				try
				{
					Univers.InputRecord();
					i++;
					Univers.InsertVector(Univers);
				}
				catch (int i)
				{
					if (i == 1){
						cout << endl << " ***Ошибка!***" << endl << "Название должно состоять только из английских букв! Пожалуйста, попробуйте еще раз." << endl;
					}
					if (i == 2){
						cout << endl << " ***Ошибка!***" << endl << "Марка инструмента должна состоять только из английских букв или цифр! Пожалуйста, попробуйте еще раз." << endl;
					}
					if (i == 3){
						cout << endl << " ***Ошибка!***" << endl << "Тут должен быть один символ - 'с' или 'g'! Пожалуйста, попробуйте еще раз." << endl;
					}
					if (i == 4){
						cout << endl << " ***Ошибка!***" << endl << "Строчка должна состоять только из цифр! Пожалуйста, попробуйте еще раз." << endl;
					}
					getch();
				}
				catch (ex1 e)
				{
					cout << e.what() << endl;
					getch();
				}
				catch (ex2 e)
				{
					cout << e.what() << endl;
					getch();
				}
				catch (...)
				{
					cout << "Критическая ошибка.";
					getch();
					exit(1);
				}
				cout << endl;
			}
			cout << endl << "Заполнение закончено! Нажмите любую клавишу для продолжения...";
			getch();
			break;
		case '2':
			Univers.ReadFromFile();//Функция извлечения информации из файла
			cout << endl << "...нажмите любую клавишу для продолжения";
			getch();
			break;
		case '3':
			toFile = false;
			Univers.ShowVector();//Вывод хранилища на экран
			cout << endl << "...нажмите любую клавишу для продолжения";
			getch();
			break;
		case '4':
			cout << "Введите название инструмента: "; cin >> srchName;
			Univers.SearchName(srchName);//Поиск записи по названию инструмента
			cout << endl << "...нажмите любую клавишу для продолжения";
			getch();
			break;
		case '5':
			Univers.SortByMark();//Сортировка по марке инструмента
			cout << endl << "***************" << endl << "Сортировка по марке инструмента: "
				<< endl << "***************" << endl;
			Univers.ShowVector();//Вывод хранилища на экран
			cout << endl << "...нажмите любую клавишу для продолжения";
			getch();
			break;
		case '6':
			Univers.ClearVector();//Функция очистки рабочей станции
			cout << endl << "Готово, нажмите любую клавишу!";
			getch();
			break;
		case '7':
			exit(0);
			break;
		default:
			cout << "Пожалуйста, введите номер '1' до '7'!";
			getch();
			break;
		}
	}
	Univers.ClearVector();
	f_In.close();
	f_Out.close();
	return 0;
}



