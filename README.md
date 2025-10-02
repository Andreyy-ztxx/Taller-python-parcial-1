# Parte A. Conceptos y lectura de código**

1) Atributos accesibles desde a: Existen A, B y D; C no. (Existen a.x, a._y y a._A__z; a.__z no).
   
2) Salida:
False True

3) Verdadero/Falso (+ explicación):
a) Falso. '_' es solo una convención; no bloquea acceso.
b) Falso. '__' aplica name mangling y puede accederse como _Clase__atrib.
c) Verdadero. El mangling usa el nombre de la clase donde se define

4) Lectura de código: imprime 'abc'. _token es solo convención y la subclase hereda ese atributo sin
restricción real.

5) Name mangling en herencia:
(2, 1)
Sub.__v -> _Sub__v = 2 y Base.__v -> _Base__v = 1

6) Identifica el error: con __slots__ = ('x',) solo se permiten atributos listados; al hacer c.y = 20 lanza
AttributeError.

7) Rellenar espacios: un “protegido por convención” empieza con '_'. Ejemplo:
self._dato = 99

8) Métodos “privados”: imprime:
True False True
Existe _step; __tick no (mangleado); _M__tick sí.

9) Acceso a __data (solo para comprobar):
print(s._S__data)

10) dir y mangling: el nombre más probable es '_D__a', porque '__a' se manglea; 'a' no es un atributo.

# Parte B. Encapsulación con @property y validación**

11) `saldo` nunca negativo:
class Cuenta:
  def __init__(self, saldo):
    self._saldo = 0
    self.saldo = saldo
    
    
@property

def saldo(self):
  return self._saldo
    @saldo.setter
    
    
def saldo(self, value):

  if value < 0:
    raise ValueError("El saldo no puede ser negativo")
    self._saldo = value
    


12) Propiedad de solo lectura temperatura_f:
    
  class Termometro:
    def __init__(self, temperatura_c):
    self._c = float(temperatura_c)
    
  @property
  
  def temperatura_f(self):
  
  return self._c * 9/5 + 32
  

13) `nombre` siempre str:
  class Usuario:
    def __init__(self, nombre):
    self.nombre = nombre
    

@property
  def nombre(self):
    return self._nombre

@nombre.setter
  def nombre(self, value):
    if not isinstance(value, str):
      raise TypeError("nombre debe ser str")
    self._nombre = value

14) Vista de solo lectura de colección:
    
  class Registro:
    def __init__(self):
    self.__items = []

  def add(self, x):
  self.__items.append(x)

  @property
  def items(self):
    return tuple(self.__items)

  **Parte C. Diseño y refactor**
  15) Motor con validación 0–200:
  class Motor:
    def __init__(self, velocidad):
    self._velocidad = 0
    self.velocidad = velocidad

  @property
  def velocidad(self):
    return self._velocidad

  @velocidad.setter
  def velocidad(self, value):
    if not (0 <= value <= 200):
      raise ValueError("velocidad debe estar entre 0 y 200")
      self._velocidad = value
  
16) ¿Cuándo usar _atributo vs __atributo?
- Usa _atributo para marcar API interna/no pública que puede cambiar; es una convención.
- Usa __atributo cuando quieras evitar colisiones de nombres en herencia (mangling) o reforzar que algo
no debe tocarse desde fuera.

17) Fuga de encapsulación y corrección:
  class Buffer:
    def __init__(self, data):
    self._data = list(data)
    
def get_data(self):
  return self._data # devuelve la lista interna (se puede mutar desde fuera)
 Corrección (devolver copia o tupla):
  def get_data(self):
  return tuple(self._data) # o: return self._data.copy()

18)Herencia y mangling: fallará en B.get porque self.__x se manglea como _B__x, distinto de _A__x.
Arreglos: acceder a self._A__x, o mejor, exponer un getter en A, o cambiar a _x.

class A:
  def __init__(self):
  self.__x = 1

def _get_x(self):
  return self.__x
  
class B(A):
def get(self):
return self._get_x()

19) Composición y fachada:
  class _Repositorio:
    def __init__(self):
  self._datos = {}
  def guardar(self, k, v):
    self._datos[k] = v
  def _dump(self):
    return dict(self._datos)
  class Servicio:
    def __init__(self):
      self.__repo = _Repositorio()
    def guardar(self, k, v):
      self.__repo.guardar(k, v)



20) Mini-kata ContadorSeguro:
  class ContadorSeguro:
    def __init__(self):
      self._n = 0
  @property
    def n(self):
      return self._n
    def __log(self):
      print("tick")
    def inc(self):
      self._n += 1
      self.__log()
   Uso básico
    c = ContadorSeguro()
    c.inc() # imprime: tick
    c.inc() # imprime: tick
    print(c.n) # 2


