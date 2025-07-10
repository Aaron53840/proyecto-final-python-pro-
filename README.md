# proyecto-final-python-pro-
mi proyecto se va a tratar de un chatbot que hable sobre los problemas que causa el calentamiento global, un tipo trivia para saber que tan conciente esta el usuario sobre el tema (calentamiento global), y pequeñas "soluciones" para tratar el calentamiento globlal

codigo: 


import discord
from discord.ext import commands
import random
import asyncio

intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix='!', intents=intents)


puntuaciones_usuario = {}
juegos_activos = {}


preguntas = [
    {
        "q": "¿Qué es lo que más contribuye al calentamiento global actualmente?",
        "a": ["Las vacas", "El transporte", "La quema de combustibles fósiles", "Los incendios forestales"],
        "correct": 2,
        "info": "La quema de carbón, petróleo y gas para producir energía es la mayor causa del calentamiento."
    },
    {
        "q": "¿Cuál de estos gases atrapa más calor en la atmósfera?",
        "a": ["Nitrógeno", "Metano", "Oxígeno", "Helio"],
        "correct": 1,
        "info": "El metano atrapa mucho más calor que el CO₂, aunque se libera en menor cantidad."
    },
    {
        "q": "¿Cuánto ha aumentado la temperatura promedio del planeta desde 1880?",
        "a": ["1.1°C", "0.8°C", "0.3°C", "2.5°C"],
        "correct": 0,
        "info": "La temperatura ha subido más de 1°C, y sigue aumentando cada década."
    },
    {
        "q": "¿Qué efecto directo tiene el deshielo de los polos?",
        "a": ["Aumenta la temperatura", "Baja el nivel del mar", "Emite oxígeno", "Cambia la salinidad del océano"],
        "correct": 3,
        "info": "El deshielo altera la salinidad del mar y puede afectar las corrientes oceánicas."
    },
    {
        "q": "¿Qué porcentaje de científicos del clima afirman que el cambio climático es causado por humanos?",
        "a": ["50%", "97%", "70%", "99.9%"],
        "correct": 1,
        "info": "El 97% de los expertos coinciden en que la actividad humana es la principal causa."
    },
    {
        "q": "¿Cuál de estas acciones individuales reduce más tu huella de carbono?",
        "a": ["Separar la basura", "No usar bolsas plásticas", "Reducir el consumo de carne", "Reciclar papel"],
        "correct": 2,
        "info": "Reducir el consumo de carne disminuye significativamente las emisiones de gases de efecto invernadero."
    },
    {
        "q": "¿Qué problema ambiental está directamente relacionado con el exceso de CO₂ en el océano?",
        "a": ["La acidificación del mar", "La lluvia ácida", "El aumento de tsunamis", "El deshielo marino"],
        "correct": 0,
        "info": "El CO₂ se disuelve en el mar, bajando el pH del agua y afectando a especies marinas."
    },
    {
        "q": "¿Cuál de estas fuentes de energía emite menos gases de efecto invernadero?",
        "a": ["Carbón", "Gas natural", "Petróleo", "Energía solar"],
        "correct": 3,
        "info": "La energía solar no emite CO₂ durante su producción de electricidad."
    },
    {
        "q": "¿Qué sector genera más emisiones globales de gases de efecto invernadero?",
        "a": ["Transporte", "Producción de energía", "Agricultura", "Industria"],
        "correct": 1,
        "info": "La generación de electricidad y calor a partir de combustibles fósiles es el sector más contaminante."
    },
    {
        "q": "¿Qué país emite más dióxido de carbono actualmente?",
        "a": ["Estados Unidos", "India", "China", "Rusia"],
        "correct": 2,
        "info": "China es el mayor emisor total de CO₂, aunque por persona emite menos que EE.UU."
    },
    {
        "q": "¿Qué efecto tiene el calentamiento global sobre los océanos?",
        "a": ["Enfría el agua", "Hace que el agua sea más potable", "Mejora la pesca", "Aumenta el nivel del mar"],
        "correct": 3,
        "info": "El aumento de temperatura derrite glaciares y expande el agua del mar, elevando su nivel."
    },
    {
        "q": "¿Qué ocurre con los corales cuando la temperatura del mar sube demasiado?",
        "a": ["Sufren blanqueamiento y pueden morir", "Cambian de color por completo", "Crean más oxígeno", "Se reproducen más rápido"],
        "correct": 0,
        "info": "El blanqueamiento coralino es una señal de estrés, y si continúa, los corales pueden morir."
    },
    {
        "q": "¿Cuál de estos hábitos ayuda más a reducir la contaminación por plástico?",
        "a": ["Comprar más ropa", "Usar botellas reutilizables", "Cocinar con gas", "Lavar el auto con manguera"],
        "correct": 1,
        "info": "Usar botellas reutilizables evita el uso de cientos de botellas plásticas de un solo uso al año."
    },
    {
        "q": "¿Cuál es una forma efectiva de ahorrar energía en casa?",
        "a": ["Usar velas", "Abrir el refrigerador seguido", "Desenchufar aparatos que no se usan", "Dejar luces encendidas por seguridad"],
        "correct": 2,
        "info": "Muchos aparatos siguen consumiendo energía aunque estén apagados, por eso es mejor desenchufarlos."
    },
    {
        "q": "¿Qué tipo de transporte ayuda más a cuidar el planeta?",
        "a": ["Automóvil particular", "Avión", "Camioneta a gasolina", "Bicicleta"],
        "correct": 3,
        "info": "La bicicleta no contamina, no gasta combustible y además mejora la salud física."
    },
    {
        "q": "¿Qué significa que los océanos se acidifiquen?",
        "a": ["Se llenan de sal", "Baja su pH y daña la vida marina", "Pierden oxígeno", "Se calientan más rápido"],
        "correct": 1,
        "info": "La acidificación ocurre cuando el CO₂ disuelto baja el pH del agua, afectando corales y moluscos."
    },
    {
        "q": "¿Cuál de estas prácticas ayuda a combatir el cambio climático?",
        "a": ["Usar bombillas incandescentes", "Desperdiciar agua", "Comprar productos locales", "Comer carne todos los días"],
        "correct": 2,
        "info": "Comprar productos locales reduce la necesidad de transporte y con eso, las emisiones."
    },
    {
        "q": "¿Qué recurso natural escaseará más con el cambio climático?",
        "a": ["El agua potable", "El oro", "El petróleo", "La arena"],
        "correct": 0,
        "info": "Las sequías más intensas y la contaminación están haciendo más difícil acceder al agua limpia."
    },
    {
        "q": "¿Qué podemos hacer con los residuos para ayudar al planeta?",
        "a": ["Botarlos al mar", "Quemarlos", "Enterrarlos en el patio", "Reciclarlos y reutilizarlos"],
        "correct": 3,
        "info": "Reciclar reduce la cantidad de basura que termina en vertederos y ahorra recursos naturales."
    },
    {
        "q": "¿Qué rol juegan los árboles en el cambio climático?",
        "a": ["Generan calor", "Absorben dióxido de carbono", "Contaminan el aire", "Emiten gases dañinos"],
        "correct": 1,
        "info": "Los árboles capturan CO₂ del aire, ayudando a reducir los gases de efecto invernadero."
    }
]


datos_clima = [
    "El calentamiento global es causado principalmente por la actividad humana. Las emisiones de gases de efecto invernadero como el dióxido de carbono (CO₂), metano (CH₄) y óxidos de nitrógeno provienen de la quema de combustibles fósiles, la deforestación y la agricultura intensiva. Estos gases atrapan el calor en la atmósfera y aumentan la temperatura global.",
    "La temperatura media del planeta ha aumentado más de 1 °C desde la era preindustrial. Aunque parezca poco, este aumento ha provocado olas de calor más intensas, sequías prolongadas, incendios y tormentas más violentas en todo el mundo.",
    "El derretimiento de los glaciares está elevando el nivel del mar. Al fundirse grandes masas de hielo como las de Groenlandia o la Antártida, se libera agua dulce al océano. Esto amenaza con inundar zonas costeras habitadas y alterar ecosistemas marinos.",
    "Los océanos están absorbiendo gran parte del exceso de calor. Se estima que han capturado más del 90% del calor adicional provocado por el cambio climático, lo que ha causado el blanqueamiento masivo de corales y cambios en las corrientes marinas.",
    "El calentamiento global afecta directamente a la agricultura y a la seguridad alimentaria. Cambios en los patrones de lluvia, sequías más frecuentes y eventos climáticos extremos pueden dañar cultivos, reducir el acceso al agua y hacer más difícil producir alimentos.",
    "El calor excesivo y la falta de agua dificultan el crecimiento de las plantas. Como resultado, puede haber menos alimentos disponibles y los precios suben, afectando especialmente a las personas con menos recursos.",
    "Muchas especies animales están en peligro por el cambio climático. Al no poder adaptarse a los cambios en su hábitat, algunas migran a otros lugares, otras reducen su población y muchas enfrentan el riesgo de extinción.",
    "El deshielo del permafrost es preocupante porque puede liberar gases como metano y dióxido de carbono. Estos gases han estado atrapados por miles de años y, al liberarse, podrían acelerar aún más el calentamiento global.",
    "La contaminación del aire y el cambio climático están estrechamente relacionados. Quemar combustibles fósiles emite tanto gases de efecto invernadero como partículas tóxicas, que dañan la salud humana y empeoran el clima.",
    "Los eventos climáticos extremos son cada vez más frecuentes e intensos. Inundaciones, huracanes, incendios y tormentas causan daños enormes a viviendas, cosechas y vidas humanas, y todo esto está conectado al calentamiento global.",
    "La acidificación de los océanos ocurre cuando el CO₂ se disuelve en el agua del mar. Esto forma ácido carbónico, que daña a los corales, moluscos y otras especies marinas, afectando también la pesca y el equilibrio ecológico.",
    "El cambio climático impacta más fuerte a los países más pobres. Estas naciones suelen tener menos recursos para adaptarse a los cambios, lo que agrava la desigualdad y deja a muchas comunidades en situación de vulnerabilidad.",
    "Existe un consenso científico claro sobre el calentamiento global. Más del 97% de los científicos del clima afirman que el cambio climático es real, está ocurriendo ahora y es causado principalmente por actividades humanas.",
    "El uso de energías renovables es fundamental para combatir el cambio climático. Sustituir el carbón, el petróleo y el gas por fuentes limpias como la solar, la eólica o la hidroeléctrica puede reducir drásticamente las emisiones.",
    "Todavía estamos a tiempo de actuar frente al cambio climático. Si reducimos nuestras emisiones, cambiamos nuestros hábitos de consumo y apoyamos políticas ambientales, podemos evitar daños mayores y proteger el planeta para el futuro."
]


consejos = [
    "Apaga las luces que no estés usando, desenchufa aparatos cuando no los necesites y usa focos LED de bajo consumo. Ahorra energía y reduce tu huella de carbono.",
    "Camina, anda en bicicleta o usa el transporte público siempre que puedas. Así ayudas a reducir la contaminación del aire y mejoras tu salud al mismo tiempo.",
    "Recicla correctamente separando papel, vidrio, plástico y latas. También puedes reutilizar materiales para evitar que terminen en vertederos.",
    "Consume menos carne y productos de origen animal. No es necesario eliminarla por completo, pero reducir su consumo disminuye el impacto ambiental.",
    "Planta árboles, cuida las áreas verdes o simplemente ten plantas en casa. Los árboles absorben dióxido de carbono y ayudan a limpiar el aire.",
    "Toma duchas más cortas, cierra la llave mientras te cepillas los dientes y repara cualquier fuga de agua. Cada gota cuenta para el planeta.",
    "Antes de comprar algo nuevo, pregúntate si realmente lo necesitas. Comprar menos reduce la contaminación y el desperdicio de recursos.",
    "Aprovecha tus dispositivos electrónicos al máximo antes de cambiarlos. Reutiliza, repara y evita caer en el consumo innecesario.",
    "Usa ropa abrigada en invierno y mantén tu casa ventilada en verano. Así puedes evitar el uso excesivo de calefacción o aire acondicionado.",
    "Habla con tu familia y amigos sobre el cambio climático. Compartir información es una forma poderosa de crear conciencia y lograr cambios reales.",
    "Compra productos locales y de temporada para reducir el transporte y la contaminación. Además, apoyas a los agricultores y economías locales.",
    "Evita el uso de plásticos de un solo uso como bolsas, cubiertos o botellas. Usa alternativas reutilizables que cuiden el medio ambiente.",
    "Participa en actividades ecológicas como limpiezas de playas, huertos comunitarios o campañas de reciclaje. Todo esfuerzo suma.",
    "Apoya a empresas y marcas comprometidas con el medio ambiente. Tus decisiones como consumidor pueden motivar a otras compañías a ser más sostenibles.",
    "Vota por líderes y propuestas que incluyan políticas para proteger el medio ambiente. La participación ciudadana es clave en la lucha contra el cambio climático."
]


datos_red = [
    "Cada correo electrónico enviado produce una pequeña cantidad de CO₂. Un correo simple genera unos 4 gramos, pero si tiene archivos adjuntos o va a muchas personas, puede llegar a 50 gramos o más. Enviar 100 correos puede equivaler a tener una bombilla encendida durante horas.",
    "Los centros de datos funcionan 24/7 y consumen muchísima energía. Estos lugares almacenan todos los contenidos de internet y emiten más CO₂ que toda la industria aérea de algunos países pequeños.",
    "Ver videos en streaming es de las actividades más contaminantes del internet. Una hora de Netflix o YouTube en HD puede generar hasta 400 gramos de CO₂, dependiendo de la red utilizada.",
    "Usar internet móvil (como 4G o 5G) contamina más que usar Wi-Fi en casa. Las antenas celulares necesitan más potencia para enviar datos, así que ver un video con datos móviles gasta más energía.",
    "Guardar fotos, correos y archivos en la nube también tiene un impacto. Cada archivo que subes se guarda en un servidor físico que consume energía constantemente.",
    "Las criptomonedas como Bitcoin usan muchísima energía. Un solo bloque de Bitcoin puede gastar la misma electricidad que una casa durante un mes, generando grandes cantidades de CO₂.",
    "Buscar algo en Google también genera emisiones. Cada búsqueda consume electricidad en varios servidores, generando entre 0.2 y 0.7 gramos de CO₂.",
    "Las videollamadas tienen huella de carbono. Una reunión en HD de una hora puede emitir más de 1 kg de CO₂. Si apagas la cámara, la emisión se reduce hasta un 60%.",
    "Usar redes sociales como TikTok, Instagram o Facebook también contamina. Cada video, historia o imagen requiere almacenamiento y transmisión constante de datos que consumen energía.",
    "Enviar memes, stickers o GIFs también genera CO₂. Aunque parezcan pequeños, cada archivo viaja desde un servidor a tu dispositivo, y eso se repite millones de veces al día en todo el mundo.",
    "Tener muchas pestañas abiertas en el navegador hace que tu computador o celular use más energía, lo que aumenta el consumo eléctrico y la huella digital.",
    "La inteligencia artificial también requiere servidores potentes que consumen bastante electricidad. Mientras más complejo sea el modelo, más energía necesita para responder.",
    "Enviar mensajes por WhatsApp, Discord u otras apps también emite CO₂. Es una cantidad pequeña por mensaje, pero a nivel global se acumula muchísimo todos los días.",
    "Las descargas y actualizaciones automáticas consumen energía. A menudo ocurren sin que te des cuenta, usando datos y electricidad innecesariamente.",
    "El spam o correos basura generan millones de toneladas de CO₂ cada año. Aunque nadie los lea, se siguen enviando, procesando y guardando en servidores que consumen energía."
]


@bot.event
async def on_ready():
    print(f"Bot {bot.user} está funcionando")

@bot.command()
async def ayuda(ctx):
    msg = """
**Bot sobre Calentamiento Global**

**Comandos disponibles:**
`!info` - Información sobre el calentamiento global
`!red` - Información sobre la contaminación de las redes
`!consejo` - Consejos para ayudar al planeta
`!trivia` - Juega una trivia de 10 preguntas
`!puntos` - Ve tus puntuaciones de la trivia
`!sobre` - Información sobre este bot
    """
    await ctx.send(msg)

@bot.command()
async def info(ctx):
    await ctx.send(random.choice(datos_clima))

@bot.command()
async def red(ctx):
    await ctx.send(random.choice(datos_red))

@bot.command()
async def consejo(ctx):
    await ctx.send(random.choice(consejos))

@bot.command()
async def trivia(ctx):
    if ctx.author.id in juegos_activos:
        await ctx.send("Ya tienes una trivia activa. Termínala primero.")
        return

    seleccionadas = random.sample(preguntas, 10)
    juegos_activos[ctx.author.id] = {
        'preguntas': seleccionadas,
        'actual': 0,
        'puntos': 0
    }

    await ctx.send(
        f"**Trivia sobre el Calentamiento Global**\n"
        f"Hola {ctx.author.mention}, te haré 10 preguntas. "
        f"Reacciona con 1️⃣ 2️⃣ 3️⃣ o 4️⃣ para responder."
    )
    await hacer_pregunta(ctx)

async def hacer_pregunta(ctx):
    juego = juegos_activos[ctx.author.id]
    actual = juego['actual']

    if actual >= len(juego['preguntas']):
        await terminar_juego(ctx)
        return

    pregunta = juego['preguntas'][actual]
    mensaje = f"**Pregunta {actual + 1}/10**\n{pregunta['q']}\n"
    for i, opcion in enumerate(pregunta['a']):
        mensaje += f"{i+1}. {opcion}\n"

    mensaje_enviado = await ctx.send(mensaje)

    reacciones = ['1️⃣', '2️⃣', '3️⃣', '4️⃣']
    for r in reacciones:
        await mensaje_enviado.add_reaction(r)

    def check(reaction, user):
        return (
            user == ctx.author and 
            str(reaction.emoji) in reacciones and 
            reaction.message.id == mensaje_enviado.id
        )

    try:
        reaccion, _ = await bot.wait_for('reaction_add', timeout=25.0, check=check)
        indice = reacciones.index(str(reaccion.emoji))
        if indice == pregunta['correct']:
            juego['puntos'] += 1
            await ctx.send(f"✅ ¡Correcto!\n{pregunta['info']}")
        else:
            correcta = pregunta['a'][pregunta['correct']]
            await ctx.send(f"❌ Incorrecto. La respuesta era: **{correcta}**\n{pregunta['info']}")
    except asyncio.TimeoutError:
        await ctx.send("⌛ Tiempo agotado para responder.")

    juego['actual'] += 1
    await asyncio.sleep(2)
    await hacer_pregunta(ctx)

async def terminar_juego(ctx):
    juego = juegos_activos[ctx.author.id]
    puntos = juego['puntos']
    del juegos_activos[ctx.author.id]

    if ctx.author.id not in puntuaciones_usuario:
        puntuaciones_usuario[ctx.author.id] = []
    puntuaciones_usuario[ctx.author.id].append(puntos)

    if puntos >= 8:
        mensaje = "🌟 ¡Excelente! Sabes mucho sobre el calentamiento global."
    elif puntos >= 5:
        mensaje = "👍 Nada mal. Puedes aprender aún más."
    else:
        mensaje = "📚 Necesitas estudiar más sobre el tema."

    consejo_final = random.choice(consejos)
    dato_clima = random.choice(datos_clima)
    dato_red = random.choice(datos_red)

    await ctx.send(
        f"**Trivia terminada**\n"
        f"Puntaje: {puntos}/10\n"
        f"{mensaje}\n\n"
        f"💡 Consejo: {consejo_final}\n\n"
        f"🌍 Dato climático: {dato_clima}\n\n"
        f"🌐 Dato digital: {dato_red}"
    )


@bot.command()
async def puntos(ctx):
    historial = puntuaciones_usuario.get(ctx.author.id, [])
    if not historial:
        await ctx.send("No has jugado aún. Usa `!trivia` para comenzar.")
        return

    total = len(historial)
    mejor = max(historial)
    promedio = sum(historial) / total
    await ctx.send(f"📊 Has jugado {total} veces.\n🔝 Mejor puntaje: {mejor}/10\n📈 Promedio: {promedio:.1f}/10")

@bot.command()
async def sobre(ctx):
    await ctx.send("Este bot fue creado para educar sobre el cambio climático. Usa `!ayuda` para comenzar.")

@bot.event
async def on_command_error(ctx, error):
    if isinstance(error, commands.CommandNotFound):
        await ctx.send("Ese comando no existe. Usa `!ayuda` para ver los disponibles.")
    else:
        print(f"Error desconocido: {error}")

bot.run('TOKEN')
