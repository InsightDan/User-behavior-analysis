#  Описание кейса
Проведён анализ данных сервиса знакомств с целью выяснить, влияет ли включение пользователей в экспериментальную группу на частоту совпадений (“match”).

### Задачи
* Загрузить и объединить данные о парах пользователей (user_id_1, user_id_2, group, is_match)

* Рассчитать для каждого пользователя общее число действий (actions), число совпадений (matchs_count) и долю совпадений (matchs_share)

* Сравнить метрики между контрольной (group=0) и тестовой (group=1) группами

* Провести t-тест для разницы в долях совпадений

* Оценить размер эффекта (Cohen’s d) и мощность теста

### Инструменты
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![Jupyter Notebook](https://img.shields.io/badge/jupyter-%23FA0F00.svg?style=for-the-badge&logo=jupyter&logoColor=white)
![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/numpy-%23013243.svg?style=for-the-badge&logo=numpy&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-%230C55A5.svg?style=for-the-badge&logo=scipy&logoColor=%white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-%23ffffff.svg?style=for-the-badge&logo=Matplotlib&logoColor=black)
![Seaborn](https://img.shields.io/badge/Seaborn-3776AB?style=for-the-badge&logo=python&logoColor=white)


### Методология
1. Загрузка данных

python
df = pd.read_csv('dating_data.csv')  # 30 000 строк с парами пользователей
2. Агрегация метрик на уровне пользователя

python
summary = (
    df.groupby(['user_id', 'group'])
      .agg(actions=('is_match','count'),
           matchs_count=('is_match','sum'))
      .assign(matchs_share=lambda x: x.matchs_count / x.actions)
      .reset_index()
)
3. Статистический анализ

* Построены распределения доли совпадений для контрольной и тестовой групп

* Проведён двухвыборочный t-тест с учётом неравенства дисперсий

* Рассчитан Cohen’s d для оценки размера эффекта

* Выполнен анализ мощности теста

T-тест:

python
result = ss.ttest_ind(
    summary.loc[summary.group==0, 'matchs_share'],
    summary.loc[summary.group==1, 'matchs_share'],
    equal_var=False
)
### Результаты
Доли совпадений:

Контрольная группа: 0.19 (19%)

Тестовая группа: 0.40 (40%)

Улучшение: +111%

Статистические характеристики:

* t-статистика: 26.48

* p-value: <1e-100 (двусторонний)

* Cohen’s d: 1.67 (большой эффект)

* Мощность теста: 1.0 (максимальная)

### Вывод
Экспериментальная группа демонстрирует значительно более высокую долю совпадений по сравнению с контролем. Большой размер эффекта (d=1.67) и высокая мощность (1.0) подтверждают надёжность результатов. Рекомендуется внедрить изменения в продуктивную среду.
