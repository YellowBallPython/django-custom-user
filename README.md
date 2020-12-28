# Usuario personalizado en Django.

#### • Necesitamos tener en cuenta que vamos a alterar archivos nativos de Django. Por ende hay que ser una serie de convenciones. 
#### • Las convenciones van a estar marcadas con este rombo =♦


# PASO 1

Como vamos a editar un modelo que viene por defecto en Django, tenemos que importarlo primero antes de modificarlo. \
Los modelos que vamos a modificar son dos AbstractBaseUser y BaseUserManager, ambos desde django.contrib.auth.models.

---

# PASO 2 

• Vamos a crear una app 'user' cuyo models.py tendrá la clase 'User(AbstractBaseUser)'. \
• Hacer este modelo lo más sencillo. Solo email y nombre + apellido. \
♦Se **_deben_** incluir los fields ⚠️is_admin, is_staff, is_active, is_superuser. Todos tipo _Bool_ solo is_active default=True, el resto False.
♦USERNAME_FIELD y REQUIRED_FIELDS son dos varibles que necesita tener nuestro modelo User.
##### USERNAME_FIELD es un string y toma el field que quermeos que el usuario use para hacer log-in. 
###### eg: `USERNAME_FIELD = 'email'`
##### REQUIRED_FIELDS es una lista y toma los fields que requerimos para completar el form.

♦Definir dos funciones `has_perm(self, perm, obj=None)` y `has_module_perms(self, app_label)`
> `has_perm(self, perm, obj=None)` return self.is_admin `has_module_perms(self, app_label)` returns `True`

---

# PASO 3

• Diseñar un administrador con el BaseUserManager, esta clase solo posee dos funciones: \
`create_user` y `create_superuser`. \
\
♦def create_user(self, email, name, password=None) - Esta función toma el USERNAME_FIELD y los strings de REQUIRED_FIELDS.
> Una vez que recibimos esos parámetros podemos asegurarnos que fue así con un if-statement:
`if not email:` `raise ValueError` lo mismo que `if not name:` \
Una vez que verificamos esto creamos el user \
`user = self.model(email=self.normalized_email(email), name=name)` \
`user.get_password(password)` \
`user.save(using=self._db)` \
`return user`\

♦def create_superuser(self, email, name, password) - Esta función toma el USERNAME_FIELD y los strings de REQUIRED_FIELDS y sirve para crear administradores.
`user = self.model(email=self.normalized_email(email), name=name, password=password)`
• También vamos a poner como True las opciones is_admin, etc.
`user.is_admin = True`
`user.is_staff = True`
`user.is_superuser = True`
`user.save(using=self._db)`
`return user`

# PASO 4

•Añadir nuestro administrador de usuarios al modelo User. \
Para eso vamos a crear una variable `objects` en el modelo User donde vamos a ejecutar la clase del BaseUserManager que contiene las dos funciones \
`create_user` y `create_superuser`.
Quedaría así = `objects = MyAccountManager()`

# PASO 5

Añadir una última línea de código en el settings.py del proyecto.
`AUTH_USER_MODEL = 'user.User'` Hay que poner aquí nuestro modelo. Por ejemplo user.User (donde la minúscula es el app y la mayúscula el modelo)
