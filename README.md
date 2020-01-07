# aprendiendo_Python
Aprendiendo Python


```python

import sys


clients = [
    {
        'name': 'Pablo',
        'company': 'Google',
        'email': 'pablo@google.com',
        'position': 'Software Engineer',
    },
    {
        'name': 'Ricardo',
        'company': 'Facebook',
        'email': 'ricardo@facebook.com',
        'position': 'Data Engineer',
    },
]


def create_client(client):
    global clients

    if client not in clients:
        clients.append(client)
    else:
        print('Client already in client\'s list')


def list_clients():
    print('uid |  name  | company  | email  | position ')
    print('*' * 50)

    for idx, client in enumerate(clients):
        print('{uid} | {name} | {company} | {email} | {position}'.format(
            uid=idx, 
            name=client['name'], 
            company=client['company'], 
            email=client['email'], 
            position=client['position']))


def update_client(client_id, updated_client):
    global clients

    if len(clients) - 1 >= client_id:
        clients[client_id] = updated_client
    else:
        print('Client not in client\'s list')


def delete_client(client_id):
    global clients

    for idx, client in enumerate(clients):
        if idx == client_id:
            del clients[idx] 
            break


def search_client(client_name):
    for client in clients:
        if client['name'] != client_name:
            continue
        else:
            return True


def _get_client_field(field_name, message='What is the client {}?'):
    field = None

    while not field:
        field = input(message.format(field_name))

    return field


def _get_client_from_user():
    client = {
        'name': _get_client_field('name'),
        'company': _get_client_field('company'),
        'email': _get_client_field('email'),
        'position': _get_client_field('position'),
    }

    return client


def _print_welcome():
    print('WELCOME TO PLATZI VENTAS')
    print('*' * 50)
    print('What would you like to do today?:')
    print('[C]reate client')
    print('[L]ist clients')
    print('[U]pdate client')
    print('[D]elete client')
    print('[S]earch client')


if __name__ == '__main__':
    _print_welcome()

    command = input()
    command = command.upper()

    if command == 'C':
        client = _get_client_from_user()

        create_client(client)
        list_clients()
    elif command == 'L':
        list_clients()
    elif command == 'U':
        client_id = int(_get_client_field('id'))
        updated_client = _get_client_from_user()

        update_client(client_id, updated_client)
        list_clients()
    elif command == 'D':
        client_id = int(_get_client_field('id'))

        delete_client(client_id)
        list_clients()
    elif command == 'S':
        client_name = _get_client_field('name')
        found = search_client(client_name)
        
        if found:
            print('The client is in the client\'s list')
        else:
            print('The client: {} is not in our client\'s list'.format(client_name))
    else:
        print('Invalid command')

```

### Librerias de siempre

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

### Importando CSV
```python
db1 = pd.read_csv('Status_data(2).csv', sep=',',parse_dates=['Fecha Contable'],low_memory=False)

db1 = pd.read_csv('LEC_VENDOR_CONDICIONES_DE_PAGO-15810031.csv', sep=',',parse_dates=['Fecha Última Modificación','Fecha/Hora Creación','Fecha Última Actividad'],encoding = "ISO-8859-1", engine='python')

db1.fillna("0")
```
### Limpiando CSV
```python
db2=db1.drop(['Nombre 2', 'Fecha Efectiva', 'Estado a Fecha Efectiva'],axis=1)
```
## Formato de fecha

#### Homogenizando fecha
```python
db2['Fecha Última Modificación'] = db2['Fecha Última Modificación'].astype('datetime64[ns]', dayfirst=True, format='%d-%m-%Y')
db2['Fecha/Hora Creación'] = db2['Fecha/Hora Creación'].astype('datetime64[ns]', dayfirst=True, format='%d-%m-%Y')
db2['Fecha Última Actividad'] = db2['Fecha Última Actividad'].astype('datetime64[ns]', dayfirst=True, format='%d-%m-%Y')
```
#### Dejando solo el mes o año
```python
db2['month'] = db2['Fecha Contable'].dt.month
db2['year'] = db2['Fecha Contable'].dt.year
```

## Convertir palabra en numero en un dataframe

#### Convertir una palabra en numero, metodo I
```python
db2['Status'] = ['1' if x == 'PER00' else '2' for x in db2['ID Set']]
db2['Status']
```

#### Convertir una palabra en numero, metodo II
```python
def map_values(row, values_dict):
    return values_dict[row]

values_dict = {'PER00': 1, 'CHL00': 2}
db2['Status2'] = db2['ID Set'].apply(map_values, args = (values_dict,))
db2['Status2']
```

#### Convertir una palabra en numero, metodo III

```python
#http://jonathansoma.com/lede/foundations/classes/pandas%20columns%20and%20functions/apply-a-function-to-every-row-in-a-pandas-dataframe/

def pais(row):
    if row["ID Set"] == "PER00":
        return "Peru"
    if row["ID Set"] == "CHL00":
        return "Chile"
    else:
        return "-"
db2 = db2.assign(Status3=db2.apply(pais, axis=1))
db2.columns
```

## Exportando dataframe a excel

```python
db2.to_excel("filtrado_por_fecha.xlsx")
```

## Dictionary en Python

### Creando la key

#### homogenizando los tipos y creando llave con ['UN'] y ['ID Proveedor']
```python
db2['Key_BU_Proveedor'] = db2['UN'].str.cat(db2['ID Proveedor'].values.astype(str))
db2['concatenate'] = (db2['ID Set'] + db2['ID Proveedor'])
```

### Dictionary Conteo de lineas

#### creando los objetos
```python
#https://www.geeksforgeeks.org/python-dictionary/
Cantidad_de_Lineas = [] #creando la lista
dictionary = {} #creando el dictionary
count=1 #conteo inicial
```

#### creando el ciclo
```python
for i in range(len(db2['Key_Comprobante_Proveedor'])):
    if db2['Key_Comprobante_Proveedor'][i] in dictionary:
        count=dictionary.get(db2['Key_Comprobante_Proveedor'][i])+1
        #Change Values dictionary
        dictionary[db2['Key_Comprobante_Proveedor'][i]]=count
        #Accessing Items dictionary
        x = dictionary.get(db2['Key_Comprobante_Proveedor'][i])
        Cantidad_de_Lineas.append(x)
    else:
        count=1
        #Change Values dictionary
        dictionary[db2['Key_Comprobante_Proveedor'][i]]=count
        #Accessing Items dictionary
        x = dictionary.get(db2['Key_Comprobante_Proveedor'][i])
        #db2['Cantidad_de_Lineas'].append(x)
        Cantidad_de_Lineas.append(x)
```

#### transformando la lista en dataframe
```python
db2['Cantidad_de_Lineas']=Cantidad_de_Lineas
```

### Creacion de dictionary desde excel

#### Leyendo el excel
```python
dicti=pd.read_excel('Dictionary.xlsx')
```
#### Transformando en listas las columnas del excel

![Dictionary excel](https://user-images.githubusercontent.com/17385297/71935127-d1970180-3184-11ea-88b4-5e82bb0c9df4.JPG)

```python
list1=dicti['Key'].tolist()
list2=dicti['Estado'].tolist()
```
#### Creando el dictionary desde 2 list
```python
#https://stackoverflow.com/questions/209840/convert-two-lists-into-a-dictionary
mapped=dict(zip(list1, list2))
type(mapped)
```

###  Primer tipo de dictionary
#### creando la lista
```python
Key_Excepcion = []
```
#### creando el ciclo
```python
for row in db4['Key_BU_Proveedor']:
    try:
        x = mapped.get(row)
        Key_Excepcion.append(x)
    except KeyError:
        x = '1'
        Key_Excepcion.append(x)
```
#### confirmando el largo
```python
len(Key_Excepcion)
```
####  transformando la lista en dataframe
```python
db4['Key_Excepcion']=Key_Excepcion
```

###  Segundo tipo de dictionary
#### creando la lista
```python
Key_Excepcion = []
```
#### creando el ciclo
```python
for row in db4['Key_BU_Proveedor']:
    #x = mapped.get(row,'never') esta opcion es en caso de no encontrar el Key, coloca 'never'
    x = mapped.get(row) #esta opcion es en caso de no encontrar el Key, coloca 'None'
    Key_Excepcion.append(x)
```
#### confirmando el largo
```python
len(Key_Excepcion)
```
####  transformando la lista en dataframe
```python
db4['Key_Excepcion']=Key_Excepcion
```

