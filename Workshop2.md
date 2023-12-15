# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил:
- Бушуев Дмитрий Эдуардович
- РИ220944
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Выбрать компьютерную игру.
- Задание 2.
- Заполнить таблицу Google Sheets.
- Задание 3.
- Настроить воспроизведение звуковых файлов.
- Выводы.

## Цель работы
Научиться с помощью Python создавать и передавать данные в таблицу Google Sheets и передавать в Unity данные из Google Sheets с помощью Python

## Задание 1
### Выбрать одну из компьютерных игр и её переменную.
Игра Dark Souls 3.  Это Action/RPG, суть которой исследовать мир, увеличивать уровень персонажа и убивать боссов. Одной из игровых переменных в данной игре является здоровье персонажа, если оно упадёт до 0, то персонаж умрёт и потеряет души(внутриигровая валюта). Если персонаж получает урон, его здоровье уменьшается, он может использовать предмет для восстановления здоровья. Диапазон допустимых значений: 0 - 1400.

![DarkSouls3](Images/DarkSouls3.png)

## Задание 2
### С помощью Python заполнить таблицу Google Sheets данными.
Ход работы:
- Написать Python код в jupyter и запустить его.
![PythonData](Images/PythonData)

```python
import gspread
import numpy as np
gc = gspread.service_account(filename='unitydatascience-408021-071220bbac3f.json') 
sh = gc.open("UnitySheets")
hp = np.random.randint(0, 1400, 11)
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(hp[i-1]))
        print(hp[i-1])
```
- Проверить полученные данные в таблице и построить диаграмму

![GoogleSheets](Images/GoogleSheets)

## Задание 3
### Настроить на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной.
Ход работы: 
- Импортировать звуковые файлы в Unity
![Sounds](Images/Sounds)

- Создать скрипт для GameObject
```cs
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
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
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
        if (dataSet["Mon_" + i.ToString()] >= 600 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 100 & dataSet["Mon_" + i.ToString()] < 600 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] <= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1nKTCFuJWtH2fmjFxcoci6Um9tCc6JcszsK_602_R1ns/values/Лист1?key=AIzaSyAXL_Yeq9GZ6He1W4B0AduBPv0q1Pnc0Do");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]));
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

- Подключить скрипт к GameObject и проверить работоспособность проекта
![Workshop2](Images/Workshop2)
## Выводы

Скачал Anaconda и Unity. Создал папку для проектов и файл python в Jupyter. Создал проект в Unity и написал скрипт, который выводит "Hello World" в консоль.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**