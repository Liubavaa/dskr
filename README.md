# Звіт

Містить опис коду та результатів

## taylor
``` python
def taylor(x_value, num):
    """
    Calculate sin(2x)^3 by Taylor's formula
    """
    start = 0
    result = 0
    x_value = (2 * x_value) ** 3
    for i in range(1, num+1):
        numerator = decimal.Decimal((x_value ** (2 * i - 1)))
        denominator = decimal.Decimal(math.factorial(2 * i - 1))
        addition = ((-1)**(i-1)) * decimal.Decimal(numerator/denominator)
        result = decimal.Decimal(addition + result)
        start += 1
    return result
```

Функція для заданого значення x і кількості членів обчислює значення sin(2x)^3 за формулою Телора. Використовується модуль decimal для точного обрахунку результатів.

## enough_membership

```python
def enough_membership(x_value, power):
    """
    Find minimum membership so that the error between the value of taylor function
    and math.sin was no more than 10 in given power
    """
    true_value = math.sin((2 * x_value) ** 3)
    membership = 1
    floor = decimal.Decimal(true_value - 10 ** power)
    roof = decimal.Decimal(true_value + 10 ** power)
    while True:
        value = taylor(x_value, membership)
        if floor < value < roof:
            return membership
        membership += 1
 ```
 
 Для заданого х і степеня піднесення 10 знаходить мінімальну кількість членів, що задовільняють дану похибку
 
 ## visualisation
 
 ```python
 membership_lst = []
    value_lst = []
    true_value = math.sin((2 * x_value) ** 3)
    for membership in range(1, num):
        value = taylor(x_value, membership)
        membership_lst.append(membership)
        value_lst.append(value)

    true_array = [true_value] * (num-1)
    for power in (0, -1, -3, -6):
        x_lim = 0
        for i, value in enumerate(value_lst):
            if true_value - 10**power < value < true_value + 10**power:
                x_lim = membership_lst[i]
                break

        plt.plot(membership_lst, value_lst, membership_lst, true_array,
                 membership_lst, [true_value - 10 ** power] * (num-1), "g--",
                 membership_lst, [true_value + 10 ** power] * (num - 1), "g--",
                 [x_lim, x_lim], [-1, 1], "r--")
        plt.grid()
        plt.ylim(true_value - 2 * (10**power), true_value + 2 * (10**power))
        if x_lim:
            plt.xlim(1, x_lim+2)
        title = "Значення з похибкою не більше 10^(" + str(power) + ")"
        plt.title(title, fontsize=11, pad=20)
        plt.legend(['Taylor', 'Math.sin', 'Окіл'])
        plt.xlabel("Кількість членів")
        plt.ylabel("Значення")
        plt.show()
```

Будує графік з прямими y = math.sin((2 * x) ** 3); y = taylor(x_value, membership), де membership - кількість членів в рокладі Тейлора; y = math.sin((2 * x) ** 3) +- 10^power, де  power = -1, -3, -6; x = x_lim, де x_lim - мінімальна кількість членів, що задовільняють похибку 10^(-1), 10^(-3), 10^(-6).

## Результати

![image](https://user-images.githubusercontent.com/92572643/155202140-d99603f9-fa97-41e2-adcc-fd3d9a4a35fa.png)

![image](https://user-images.githubusercontent.com/92572643/155202157-99712b0f-4842-4d51-b5cf-4b6ad0cc049c.png)

![image](https://user-images.githubusercontent.com/92572643/155202178-67a90e0d-3297-4ce7-9f64-9d0b4d3bb3fd.png)

Лінія червоним пунктиром - це межа, яка вказує мінімальну кількість членів ряду Тейлора, при якому значення функції taylor входить в заданий окіл.

## P.S.
Графік створений функцією Тейлора 'рухається' зигзагом навколо прямої y = math.sin((2 * x) ** 3), і з збільшенням кількості членів наближається до неї.
