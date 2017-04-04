---
layout: post
title:  "Autenticación API con Phoenix y Guardian"
date:   2017-03-08 09:31:34 -0600
categories: elixir
featured_image: "/assets/img/elixir/phoenix_post_01.jpg"
featured_video:
---

<a href="https://github.com/ueberauth/guardian">Guardian</a> es una biblioteca para usar autenticación en aplicaciones hechas con Phoenix. Con Guardian podemos crear <span style="line-height: 1.5;">JSON Web Tokens que se pueden usar para autenticar al usuario ya sea usando EmberJS, Angular, iOS, Android, etc.</span>

Guardian no solo sirve para generar tokens, si no también para limitar quienes ingresan a una URL o no.

En este tutorial crearemos un proyecto en donde generaremos los tokens para los usuarios, pude ser el inicio para crear tus API Rest con Phoenix.

<h2>Creando el proyecto</h2>

Primero vamos a crear un proyecto Phoenix, como nuestra aplicación será solamente para generar APIs no es necesario el manejo de assets estáticos.

<pre class="lang:sh decode:true">mix phoenix.new hello_guardian --no-brunch</pre>

Escribe Y y dale enter para que instale las dependencias

{% highlight bash %}
...
* creating hello_guardian/web/templates/page/index.html.eex
* creating hello_guardian/web/views/layout_view.ex
* creating hello_guardian/web/views/page_view.ex

Fetch and install dependencies? [Yn]
{% endhighlight %}

Una vez hecho esto, ya tendremos nuestra aplicación de Phoenix.

<h2>Creando los modelos y base de datos</h2>

Lo primero que haremos será configurar nuestra base de datos y crear el modelo de usuario para hacer las pruebas

{% highlight elixir %}

# Configure your database

config :hello_guardian, HelloGuardian.Repo,
adapter: Ecto.Adapters.Postgres,
username: "postgres",
password: "postgres",
database: "hello_guardian_dev",
hostname: "localhost",
pool_size: 10
{% endhighlight %}

Ya que lo hayas configurado, crearemos un modelo de usuario sencillo, que nos servirá para poner en práctica el login con tokens.
En la carpeta <code>web/models/</code> crea el archivo <code>user.ex</code>.

{% highlight elixir %}
defmodule HelloGuardian.User do
  use HelloGuardian.Web, :model

  schema "users" do
    field :name, :string
    field :email, :string
    field :password, :string
    field :password_conf, :string, virtual: true
    timestamps
  end

  @required_fields ~w(email password password_conf)
  @optional_fields ~w(name)

  def changeset(model, params \\ :empty) do
    model
    |> cast(params, @required_fields, @optional_fields)
    |> unique_constraint(:email)
  end
end
{% endhighlight %}

Un modelo simple, hemos creado el schema "user" que será el nombre de nuestra tabla en la base de datos. el campo <code>password_conf</code>
es virtual ya que necesitamos que podamos hacer una validación de la contraseña pero no queremos guardar este dato en la base de datos, por
lo que solo existirá en nuestra aplicación. También hemos hecho que el email sea único, de tal forma que no se repita en nuestra base de
datos, esto servirá para que sea usado como nombre de usuario.

Es tiempo de crear la migración para crear la tabla en la base de datos.

{% highlight bash %}
mix ecto.gen.migration create_user
{% endhighlight %}

Abre el archivo que generó la migración para crear nuestra tabla y escribe lo siguente

{% highlight elixir %}
defmodule HelloGuardian.Repo.Migrations.CreateUser do
  use Ecto.Migration

  def change do
    create table(:users) do
      add :name, :string
      add :email, :string
      add :password, :string
      timestamps
    end

    create unique_index(:users, [:email])
  end
end
{% endhighlight %}

Ahora vamos a crear un usuario de prueba en nuestra base de datos, en el archivo `priv/repo/seeds.exs` crea el siguiente código

{% highlight elixir %}
HelloGuardian.Repo.insert!(%User{
  name: "Giovanni",
  email: "test@example.com",
  password: "12345678"
})
{% endhighlight %}

Ya tenemos nuestras primeras configuraciones listas, vamos a crear la base de datos, a migrarlo y a insertar los datos.

{% highlight bash %}
$ mix ecto.create
The database for HelloGuardian.Repo has been created.

$ mix ecto.migrate

13:18:40.201 [info] == Running HelloGuardian.Repo.Migrations.CreateUser.change/0 forward

13:18:40.202 [info] create table users

13:18:40.234 [info] create index users_email_index

13:18:40.241 [info] == Migrated in 0.3s

{% endhighlight %}

Ahora corre <code>mix run priv/repo/seeds.exs</code> en la consola, con esto habremos llenado nuestra base de datos.

Listo, ya tenemos creada nuestra tabla y hemos agregado un usuario de prueba. Como te darás cuenta, el campo de password se encuentra en texto plano, esto obviamente no lo haremos
cuando vayamos a crear una aplicación ya para producción, así que vamos a cambiar nuestro código para que la contraseña esté hasheada.

<h2>Hasheando el campo de contraseña</h2>

Para que nuestra contraseña esté hasheada, vamos a necesitar una biblioteca llamada comeonin.

Agrega la biblioteca a tus dependencias en <code>mix.exs</code>

{% highlight elixir %}
{:comeonin, "~> 3.0"}
{% endhighlight %}

Corre `mix do deps.get, compile`

Ahora ya tenemos comeonin instalado, ahora es necesario actualizar nuestro código para que guarde la contraseña hasheada. Borra el registro que creamos, y actualiza el arcivo <code>seeds.exs</code>

{% highlight elixir %}
alias HelloGuardian.User
  import Comeonin.Bcrypt, only: [hashpwsalt: 1]

  HelloGuardian.Repo.insert!(%User{
    name: "Giovanni",
    email: "test@example.com",
    password: hashpwsalt("12345678"),
    password_conf: "12345678",
  })
{% endhighlight %}

Como puedes ver importamos la función hashpwsalt, esto nos sirve para hashear la contraseña. Si corremos ahora este script <code>mix run priv/repo/seeds.exs</code> y vemos el campo de contraseña en
nuestra base de datos, te podrás dar cuenta que el campo contraseña se encuentra hasheado.

<h2>Instalando y configurando Guardian</h2>

Ahora es tiempo de instalar y configurar Guardian, que es la biblioteca que usaremos para crear los tokens que usaremos ya sea en conjunto
con el frontend o en alguna aplicación móvil.

Primero agregamos guardian a nuestras dependencias

{% highlight elixir %}
defp deps do
  [# ...
    {:guardian, "~> 0.10.0"}
  ..
  ]
end
{% endhighlight %}

Corre <code>mix deps.get</code> para obterlo. Antes de poder usar Guardian, es necesario hacer unas configuraciones, en el archivo <code>config.exs</code> agrega
lo siguiente

{% highlight elixir %}
config :guardian, Guardian,
  allowed_algos: ["HS512"], # optional
  verify_module: Guardian.JWT, # optional
  issuer: "HelloGuardian",
  ttl: { 30, :days },
  verify_issuer: true, # optional
  secret_key: "jkjjsisisi*jsj0(=0",
  serializer: HelloGuardian.GuardianSerializer
{% endhighlight %}

Con esto, configuramos aspectos básicos para generar nuestro token, usamos un algoritmo HS512, también la duración que permitirá
estar activo el token, en nuestro caso es 30 días. La secret key debes poner una cadena que sea difícil de adivinar, por ahora
con poner cualquiera está bien, en producción querrás poner estas configuraciones fuera del source control.

También necesitamos crear un serializer, que nos ayudará an codificar y decodificar nuestro usuario y token.

Crea el archivo <code>lib/hello_guardian/guardian_serializer.ex</code>

{% highlight elixir %}
defmodule HelloGuardian.GuardianSerializer do
  @behaviour Guardian.Serializer

  alias HelloGuardian.Repo
  alias HelloGuardian.User

  def for_token(user = %User{}), do: { :ok, "User:#{user.id}" }
  def for_token(_), do: { :error, "Unknown resource type" }

  def from_token("User: " <> id), do: { :ok, Repo.get(User, id) }
  def from_token(_), do: { :error, "Unknown resource type" }
end
{% endhighlight %}

Ya con Guardian configurado, es hora de crear nuestro controlador para general el token.

<h2>Creando el controlador de login</h2>

En la carpeta de controllers, crea el archivo `login_controller.ex`

{% highlight elixir %}
defmodule HelloGuardian.LoginController do
  use HelloGuardian.Web, :controller
  alias HelloGuardian.User

  import Comeonin.Bcrypt, only: [checkpw: 2]

  def login(conn, %{"user" => user_params}) do
    
    %{"email" => email, "password" => password} = user_params
    
    if user = validate_credentials(email, password) do
      # Get the token from Guardian
      new_conn = Guardian.Plug.api_sign_in(conn, user)
      jwt = Guardian.Plug.current_token(new_conn)
      claims = Guardian.Plug.claims(new_conn)

      new_conn
      |> json(%{token: jwt)
    else
      conn
      |> put_status(400)
      |> json(%{error: "Correo o contraseña incorrecta"})
    end
  end

  defp validate_credentials(email, password) do
    if user = User.get_by_email(email) do
      (checkpw(password, user.password)) && user || nil
    else
      nil
    end
  end
end
{% endhighlight %}

Lo que hicimos aquí es sencillo, creamos el alias hacia nuestro modelo de usuario para poder usarlo en nuestro código,
igualmente importamos la función <code>checkpw/2</code> que nos ayuda a verificar si el password que enviamos es el mismo
que el que tenemos en la base de datos.

Para ayudarnos a saber si el usuario y contraseña son válidos, creamos la función privada <code>validate_credentials/2</code> que toma el correo
y contraseña, primero hacemos la búsqueda por email con la función <code>get_by_email</code>, que aún no hemos creado en el modelo, si el
usuario existe, entonces checamos si el password es igual al guardado, si el usuario no es nil, si
pasa todas esas validaciones entonces nos valida la credencial.

Una vez que hemos validado la credencial, entonces es hora de obtener el token, la parte importante en esto es

{% highlight elixir %}
new_conn = Guardian.Plug.api_sign_in(conn, user)
jwt = Guardian.Plug.current_token(new_conn)
claims = Guardian.Plug.claims(new_conn)

new_conn
|> json(%{token: jwt})
{% endhighlight %}

Al usar la función <code>Guardian.Plug.current_token(new_conn)</code> podemos optener el token que devolvemos en el json.

Y como cada controlador necesita su vista, crea el archivo <code>login_view.ex</code>

{% highlight elixir %}
defmodule HelloGuardian.LoginView do
  use HelloGuardian.Web, :view
end
{% endhighlight %}

<h2>Obteniendo el usuario por email</h2>

Como dije anteriormente, nos hace falta crear la función <code>get_by_email/1</code>, en tu modelo de usuario, crea la función

{% highlight elixir %}
alias HelloGuardian.User

def get_by_email(email) do
  query = from user in User,
    where: user.email == ^email

  query
  |> HelloGuardian.Repo.one
end
{% endhighlight %}

Simplemente hacemos una consulta en los usuarios con el correo que pasemos, si encuentra uno o más, enviamos el primero, que sería
el único ya que no debe haber dos o más usuarios con el mismo email.

<h2>Configurando el router</h2>

Ya casi terminamos, antes de obtener el token, es necesario decirle a nuestro router a qué path debemos ir, pero antes, hay que
configurar nuestro pipeline de Api, ya que estamos haciendo un API Rest

{% highlight elixir %}
pipeline :api do
  plug :accepts, ["json"]
  plug Guardian.Plug.VerifyHeader
  plug Guardian.Plug.LoadResource
end
{% endhighlight %}

Simplemente verificamos el encabezado y cargamos los recursos de Guardian,

Ahora en nuestro scope de api, vamos a poner el post a nuestra URL de login

{% highlight elixir %}
scope "/api", HelloGuardian do
  pipe_through :api

  post "/login", LoginController, :login
end
{% endhighlight %}

Listo, ya tenemos terminado nuestro generador de tokens

<h2>Probando nuestro login</h2>

Ahora es tiempo de probar nuestra aplicación.

Para probarlo, yo uso Postman, que es una aplicación de Google que nos sirve para hacer requests

Primero corremos la aplicación <code>mix phoenix.server</code>

Ahora usamos Postman para hacer el request, la URL que usaremos será <code>http://localhost:4000/api/login</code> y en el body
del request usamos un json con los datos del usuario

{% highlight javascript %}
{"user":
{
"password":"12345678",
"email":"test@example.com",
}

}
{% endhighlight %}

<img src="https://res.cloudinary.com/gidrek/image/upload/c_scale,w_836/v1457562189/login_post.png" alt="Post to login" />

Y con los datos correctos, obtenemos nuestro token

<img src="https://res.cloudinary.com/gidrek/image/upload/c_scale,w_836/v1457562222/token.png" alt="Getting token" />

Ahora este token podemos usarlo en el header de Autorización, de tal forma que no enviamos el usuario y contraseña
en cada request, para poder usar ese token y que solo puedan entrar los usuarios logueados es necesario hacer algunos ajustes
en los controladores, que también se hacen con Guardian, pero eso sería para otro post.

Ya con esto terminamos, si tienes alguna duda o comentario con respecto al tutorial, puedes escribirme.
