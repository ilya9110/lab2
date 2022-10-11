# lab2
# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Маслий Илья Андреевич
- РИ211102
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Цель работы
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Задание 1
Ход работы:
Реализовать совместную работу и передачу данных в связке Python- Google-Sheets – Unity.
- В облачном сервисе google console подключить API для работы с google sheets и google drive.
![image](https://user-images.githubusercontent.com/29748577/195107335-7c979093-39b1-4fb4-a69c-a517345381e9.png)
- Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.
Генерация чисел: ![image](https://user-images.githubusercontent.com/29748577/195107706-e020459b-37e7-48e9-8e03-563cd91662ed.png)
Запись в гугл таблицу: ![image](https://user-images.githubusercontent.com/29748577/195107979-3f663f9f-0201-4c33-8485-f3b464e44fd5.png)
- Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.
![image](https://user-images.githubusercontent.com/29748577/195108582-c5e38666-3fb2-4992-a507-1751fefca984.png)
- Написать функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы.
Скриншот из Unity: ![image](https://user-images.githubusercontent.com/29748577/195109203-0de58b92-eaf3-40d8-9b99-d73acbb6c693.png)
Код скрипта:
```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1Uvav6XaYcGg3mDLhNo8hEkHhsjnl72Ar_S51Y_qFCQc/values/Лист1?key=AIzaSyD_iIQAKPc3KEGTseo2la69RLaPQ8B_gY0");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```

## Задание 2
### В разделе «ход работы» пошагово выполнить каждый пункт сописанием и примером реализации задачи по теме лабораторной работы.
Ход работы:
- Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.

```py

#Import the required modules, numpy for calculation, and Matplotlib for drawing
import numpy as np
import matplotlib.pyplot as plt
#This code is for jupyter Notebook only
%matplotlib inline
# define data, and change list to array
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)
#Show the effect of a scatter plot
plt.scatter(x,y)

```
Скриншот: 
![image](https://user-images.githubusercontent.com/29748577/192552068-541f2f24-5ed0-4dae-a1b3-5d27a55e6d55.png)

- Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.

```py

#The basic linear regression model is wx+ b, and since this is a two-dimensional space, the model is ax+ b
def model (a, b, x):
    return a*x + b
#Tahe most commonly used loss function of linear regression model is the loss function of mean variance difference 
def loss_function (a, b, x, y):
    num = len (x)
    prediction = model(a, b, x) 
    return (0.5/num) * (np.square (prediction-y)).sum()
#The optimization function mainly USES partial derivatives to update two parameters a and b
def optimize (a, b, x, y) :
    num = len (x)
    prediction = model (a, b,x)
    #Update the values of A and B by finding the partial derivatives of the loss
    da = (1.0/num) * ((prediction -y)*x).sum()
    db = (1.0/num) * ((prediction -y).sum ())
    a = a - Lr*da
    b = b - Lr*db
    return a, b

#iterated function, return a and b 
def iterate (a,b, x, y, times):
    for i in range (times):
        a,b = optimize (a,b,x,y)
    return a, b

```

Скриншот: ![image](https://user-images.githubusercontent.com/29748577/192554246-7e076015-3c0e-40f9-860a-0153211db1d8.png)


- Начать итерацию
- Шаг 1 Инициализация и модель итеративной оптимизации

```py

#Initialize parameters and display
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001
#For the first iteration, the parameter values, losses, and visualization after the
iteration are displayed
a,b = iterate(a,b,x,y,1)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```
Скриншот: ![image](https://user-images.githubusercontent.com/29748577/192559371-0a01bef7-d18b-47c4-ab8b-7ea6f27b5c02.png)


- Шаг 2 На второй итерации отображаются значения параметров, значения потерь и эффекты визуализации после итерации

```py

a,b = iterate (a, b, x, y, 2)
prediction=model (a, b, x)
loss = loss_function (a, b, x, y)
print (a,b, loss) 
plt.scatter (x, y)
plt.plot (x,prediction)

```
Скриншот: ![image](https://user-images.githubusercontent.com/29748577/192559590-988681d1-0394-46bc-8b5e-5c87d5d89884.png)

- Шаг 3 Третья итерация показывает значения параметров, значения потерь и визуализацию после итерации

```py

a,b = iterate (a,b,x,y,3)
prediction=model (a, b, x)
loss = loss_function (a,b,x,y)
print (a, b, loss) 
plt.scatter (x, y)
plt.plot (x,prediction)

```
Скриншот: ![image](https://user-images.githubusercontent.com/29748577/192560738-3109b9ce-9335-4747-96c2-013bfd9a60b2.png)

- Шаг 4 На четвертой итерации отображаются значения параметров, значения потерь и эффекты визуализации

```py

a,b = iterate (a,b,x,y,4)
prediction=model (a, b, x)
loss = loss_function (a,b,x,y)
print (a, b, loss) 
plt.scatter (x, y)
plt.plot (x,prediction)

```
Скриншот: ![image](https://user-images.githubusercontent.com/29748577/192561086-f491f7d4-69ca-40e8-8bb5-ba93b9eda233.png)

- Шаг 5 Пятая итерация показывает значение параметра, значение потерь и эффект визуализации после итерации

```py

a,b = iterate (a,b,x,y,5)
prediction=model (a, b, x)
loss = loss_function (a,b,x,y)
print (a, b, loss) 
plt.scatter (x, y)
plt.plot (x,prediction)

```
Скриншот: ![image](https://user-images.githubusercontent.com/29748577/192561510-6fe7c26c-e8ce-440d-92f7-021f61239d31.png)

- Шаг 6 10000-я итерация, показывающая значения параметров, потери и визуализацию после итерации

```py

a,b = iterate (a,b,x,y,10000)
prediction=model (a, b, x)
loss = loss_function (a,b,x,y)
print (a, b, loss) 
plt.scatter (x, y)
plt.plot (x,prediction)

```
Скриншот: ![image](https://user-images.githubusercontent.com/29748577/192562236-0d1a14a0-7de8-4501-a027-29f439056397.png)

## Выводы

Я ознакомился с основными операторами зыка Python на
примере реализации линейной регрессии.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
