# Tabica2.0
О чистке наименований продуктов

#Загружаем библиотеки
#Создаем словарь для транслитерации

import string

def translit1(string):
    """ This function works just fine """
    capital_letters = {
        u'А': u'A',
        u'Б': u'B',
        u'В': u'V',
        u'Г': u'G',
        u'Д': u'D',
        u'Е': u'E',
        u'Ё': u'E',
        u'Ж': u'Zh',
        u'З': u'Z',
        u'И': u'I',
        u'Й': u'Y',
        u'К': u'K',
        u'Л': u'L',
        u'М': u'M',
        u'Н': u'N',
        u'О': u'O',
        u'П': u'P',
        u'Р': u'R',
        u'С': u'S',
        u'Т': u'T',
        u'У': u'U',
        u'Ф': u'F',
        u'Х': u'H',
        u'Ц': u'Ts',
        u'Ч': u'Ch',
        u'Ш': u'Sh',
        u'Щ': u'Sch',
        u'Ъ': u'',
        u'Ы': u'Y',
        u'Ь': u'',
        u'Э': u'E',
        u'Ю': u'Yu',
        u'Я': u'Ya'
    }

    lower_case_letters = {
        u'а': u'a',
        u'б': u'b',
        u'в': u'v',
        u'г': u'g',
        u'д': u'd',
        u'е': u'e',
        u'ё': u'e',
        u'ж': u'zh',
        u'з': u'z',
        u'и': u'i',
        u'й': u'y',
        u'к': u'k',
        u'л': u'l',
        u'м': u'm',
        u'н': u'n',
        u'о': u'o',
        u'п': u'p',
        u'р': u'r',
        u'с': u's',
        u'т': u't',
        u'у': u'u',
        u'ф': u'f',
        u'х': u'h',
        u'ц': u'ts',
        u'ч': u'ch',
        u'ш': u'sh',
        u'щ': u'sch',
        u'ъ': u'',
        u'ы': u'y',
        u'ь': u'',
        u'э': u'e',
        u'ю': u'yu',
        u'я': u'ya'
    }

    translit_string = ""

    for index, char in enumerate(string):
        if char in lower_case_letters.keys():
            char = lower_case_letters[char]
        elif char in capital_letters.keys():
            char = capital_letters[char]
            if len(string) > index+1:
                if string[index+1] not in lower_case_letters.keys():
                    char = char.upper()
            else:
                char = char.upper()
        translit_string += char

    return translit_string
}

#Читаем табицу из csv
#Разделяем пробелами заглавные буквы
{ 
product_matrix=[]

for j in product:
    textnew = ("")
    k = 1
    for i in j:
        if (i).isalpha()==True:                    #Буква ли?
            if (i).istitle()==False:               #Заглавная ли буква?
                textnew = textnew + (i)
                k=0
            else:
                k= k + 1
                if k < 2:                          #Предыдущая буква просная?
                    textnew = textnew + " " + (i)
                else:
                    textnew = textnew + (i)
        else:
            k=k+1
            textnew = textnew + (i)
    textnew = textnew.strip()
    product_matrix.append((textnew.replace("  ", " ")))
 }
#Переводим в прописную латиницу
{
_1_product_name_translit=[]

for a in product_name_departure:
    a=a.lower()
    _1_product_name_translit.append(translit1(a))
_1_product_name_translit
}
#Разрезаем наименования на элементы
{
import re

#_2_product_name_split = []


for a in _1_product_name_translit:
    #print (a)
    a = str(a)
    _2_product_name_split.append(re.split('; |, |\*|\n|\-|\.|\s|/|/. ',a))
    
_2_product_name_split
}
#Раскладываем матрицу на биграммы
{
lines = range(0,len(example))
parameter = range(0,2)

er=0

for p in parameter:
    for i in lines:
        if example[i][p]!="":
            index=range(0,len(example[i]))
            for j in index:
                if len(example[i][j])>1:                     #пропускаем значения меньше 2 символов
                    if p != j:
                        print (example[i][p], example[i][j]) #parameter,
                else:
                    er=er+1
        else:
            er=er+1
#print("пропущенных: "+str(er))
}
#Проводим оценку вложенности
#Считаем метрики типа TF-IDF
#Создаем словарь из устоявшихся биграмм (n-gramm?)
{
brand_dic={"a":"A"}
taste_dic={"b":"B"}
size_dic={"c":"C"}
}
#Обозначаем синтаксис конечного названия
{
feature=(brand_dic, taste_dic, size_dic)
}
#Собираем отформатированные названия
#Проводим оценку метрик качества
