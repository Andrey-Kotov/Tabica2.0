#TABICA

#Загружаем библиотеки

import string #для транлитерации
import re
import pandas

#Создаем словарь для транслитерации

def translit(string):
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
	
	
#Читаем табицу из csv (utf-8 без BOM)

tabica = (pandas.read_csv("Tabica_res.csv", sep=";"))

#Разделяем пробелами слова верхнего регистра и числа, что справа от букв

product_name_split_by_register=[]
product_name_first=list(tabica.product_name_first)

for j in product_name_first:
    textnew = ("")
    register = 1                                   #Счетчик регистра
    letter = 0                                     #Счетчик букв
    number = 0
    for i in j:
        if (i).isalpha()==True:                    #Если буква то:
            letter = 1
            number = 0
            if (i).istitle()==False:               #Если не заглавная то:
                textnew = textnew + (i)
                register = 0
            else:                                  #Если строчная то:
                register= register + 1
                if register < 2:                   #Если предыдущая буква не заглавная то:
                    textnew = textnew + " " + (i)
                else:                              #Если предыдущая буква заглавная то:
                    textnew = textnew + (i)
        else:                                      
            register = register + 1
            if (i).isdigit()==True:
                if number<letter:
                    textnew = textnew + " " + (i)
                    letter = 0
                    number = 1
                else:
                    textnew = textnew + (i)
            else:
                textnew = textnew + (i)
    textnew = textnew.strip()
    product_name_split_by_register.append((textnew.replace("  ", " ")))
	

#Переводим в прописную латиницу

product_name_translit=[]

for a in product_name_split_by_register:
    a=a.lower()
    product_name_translit.append(translit(a))
	
#Разрезаем наименования на элементы

product_name_split = []

for a in product_name_translit:
    #print (a)
    a = str(a)
    product_name_split.append(re.split('; |, |\*|\n|\-|\.|\s|/|/. ',a))

#Строим матрицу [параметр, состав описания]

tabica_split_name = []
a=list()
i=0

for element in product_name_split:
    a = list(element)
    if tabica.category1[i]!="-":
        a.insert(0, tabica.category1[i])
        tabica_split_name.append(a)
    i=i+1

#Раскладываем матрицу на биграммы

lines = range(0,len(tabica_split_name))
parameter = range(0,1)
dic_bgramm = []
dic_ogramm = []

er=0

for p in parameter:
    for i in lines:
        if tabica_split_name[i][p]!="-":
            index=range(0,len(tabica_split_name[i]))
            for j in index:
                if len(tabica_split_name[i][j])>1:             #пропускаем значения меньше 2 символов
                    if p != j:
                        bgramm = []
                        bgramm.append(tabica_split_name[i][p]) 
                        bgramm.append(tabica_split_name[i][j]) #parameter,
                        dic_bgramm.append(bgramm)
                        dic_ogramm.append(bgramm[1])
                else:
                    er=er+1
        else:
            er=er+1

#Собираем модель для словаря, подсчитываем частоту пар и исключений

dic_scheme=[]
dic_value=[]
dic=[]
j=0

dic_bgramm.sort()

for i in dic_bgramm:
    j=j+1
    if i!=dic_bgramm[j-2]:         #Пропускаем повторы
        dic_gram = []
        b=dic_bgramm.count(i,)     #Считаем частоту пары
        o=dic_ogramm.count(i[1])   #Считаем кол-во исключений
        if b>=o>2:                 # b<=o>1
            dic_gram.append(i)
            dic_gram.append(b)
            #dic_gram.append(o)
            dic_scheme.append(dic_gram)
            dic.append([i[1],i[0]]) #Собираем словарь, изменив положение на [ключ: значение]
            dic_value.append([i[1],b])

dic=dict(dic)
dic_value=dict(dic_value)

#Создаем столбец по описаниям

for i in product_name_split:
    clarity = []
    for j in i:
        clarity.append([dic_value.get(j,(0)),j])
        clarity.sort(reverse=True)
    if clarity[0][0]!=0:
        print(dic[clarity[0][1]])
    else:
        print('-')



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
