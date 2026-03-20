import React, { useState, useEffect } from 'react';
import { 
  Shield, Sword, Navigation, AlertCircle, Edit2, Save, X, Activity, 
  MapPin, Award, Briefcase, RotateCcw, CheckSquare, Square, ChevronDown, 
  Info, ArrowUpCircle, CheckCircle2, Target, PieChart, Map, Crosshair, 
  BookOpen, Zap, Download, Upload, Copy, Database, Trophy, TrendingUp, Calculator
} from 'lucide-react';

// --- BASE DE DATOS DE EVENTOS Y LUGARES ---
const GAME_EVENTS = [
  { id: 'johto_early', label: 'Johto: Inicio (Medallas 1-4)', tips: ["Atrapa un Gastly en la Torre Bellsprout de noche.", "Salva a los Slowpoke en Pueblo Azalea para obtener la Bola Humo/Carbón.", "Usa a Geodude/Onix contra Blanca y su Miltank para resistir Desenrollar."] },
  { id: 'midgame', label: 'Johto: Desarrollo (Medallas 5-7)', tips: ["Visita el Lago de la Furia (Ruta 43) para atrapar al Gyarados Rojo.", "Obtén el Repartir Exp del Sr. Pokémon dándole la Escama Roja.", "Eevee te espera con Bill en Ciudad Trigal después de verlo en Ciudad Iris."] },
  { id: 'guarida_dragon', label: 'Johto: Guarida Dragón', tips: ["Usa Surf y Torbellino para llegar al santuario en el centro.", "Responde con COMPASIÓN y AMISTAD a las preguntas del Anciano.", "Asegúrate de tener un espacio libre en el equipo para recibir a Dratini con Velocidad Extrema."] },
  { id: 'liga_pokemon', label: 'Liga Pokémon (Alto Mando)', tips: ["Nivel recomendado: 45 a 50.", "Will: Usa ataques Siniestro/Fantasma.", "Koga: Usa Psíquico/Fuego.", "Bruno: Lucha/Agua/Volador.", "Karen: Fantasma/Siniestro/Lucha.", "Lance: Hielo/Roca (¡Lleva ataques de Hielo para sus 3 Dragonite!).", "🚨 CUIDADO: Quítate la Banda Focus, se perderá permanentemente si se usa aquí."] },
  { id: 'kanto_pt1', label: 'Kanto P1: Llegada y Primeros Líderes', tips: ["Toma el S.S. Aqua desde Ciudad Olivo a Ciudad Carmín.", "Vence a Lt. Surge en Carmín (lleva tipo Tierra).", "Ve a Ciudad Azafrán por Sabrina (Fantasma/Siniestro) y a Azulona por Erika (Fuego/Volador).", "Habla con la Copiona en Azafrán para iniciar la misión del muñeco Clefairy."] },
  { id: 'kanto_pt2', label: 'Kanto P2: Central Energía y Snorlax', tips: ["Ve a la Central Energía en la Ruta 10 y habla con el director.", "Recupera la Maquinaria del Team Rocket en el Gimnasio Celeste.", "Vence a Misty en el Cabo Celeste (Ruta 25).", "Consigue la Tarjeta Expansión de Radio en Pueblo Lavanda.", "Despierta a Snorlax (Ruta 11) sintonizando la Pokéflauta en tu Pokégear (canal superior)."] },
  { id: 'kanto_pt3', label: 'Kanto P3: Últimas Medallas', tips: ["Ve a Ciudad Fucsia y vence a Janine (Veneno).", "Surfea al sur de Isla Canela hasta las Islas Espuma para vencer a Blaine (Fuego).", "Habla con Blue en Isla Canela y luego véncelo en Ciudad Verde.", "Ve con el Prof. Oak tras las 16 medallas para que te dé acceso al Monte Plateado."] },
  { id: 'red', label: 'Monte Plateado (Batalla Final vs. Red)', tips: ["Nivel recomendado: 80+.", "El clima será Granizo (Restos no te curará a menos que uses Día Soleado/Danza Lluvia).", "Cuidado con Pikachu Nv. 88 (Placaje Eléctrico), lleva un tipo Tierra veloz o con robustez.", "Snorlax es una muralla, usa ataques Lucha físicos de alta potencia (A Bocajarro, Fuerza Bruta)."] }
];

const LOCATION_TRIVIA = {
  "Pueblo Primavera": "Hogar del Prof. Elm. El viento sopla el inicio de tu aventura.",
  "Ciudad Cerezo": "El Sr. Pokémon vive cerca (Ruta 30), dale la Escama Roja por el Repartir Exp.",
  "Ciudad Malva": "Gimnasio Volador. Cambia un Bellsprout por un Onix en una casa cerca del Centro.",
  "Pueblo Azalea": "Gimnasio Bicho. ¡Salva a los Slowpoke! Tras el encinar, te dan el objeto Carbón.",
  "Ciudad Trigal": "Gimnasio Normal. Visita a Bill para conseguir a Eevee. ¡Cuidado con el Miltank de Blanca!",
  "Ciudad Iris": "Gimnasio Fantasma. La Torre Quemada alberga a los perros legendarios.",
  "Ciudad Olivo": "Gimnasio Acero. Necesitas la Medicina de Orquídea para curar al Ampharos del faro.",
  "Ciudad Orquídea": "Gimnasio Lucha. Recibe la MO Vuelo de la esposa de Aníbal tras vencerlo.",
  "Pueblo Caoba": "Gimnasio Hielo. En el Lago de la Furia (Noreste) están las Gafas Elegid.",
  "Ciudad Endrino": "Gimnasio Dragón. La Guarida Dragón esconde un Dratini (Vel. Extrema) si respondes bien al Anciano.",
  "Meseta Añil": "¡La Liga Pokémon! Compra Restaurar Todo. Nivel recomendado: 45+.",
  "Ciudad Carmín": "Gimnasio Eléctrico. Busca a Máximo aquí para liberar a Latias/Latios (Errantes).",
  "Pueblo Lavanda": "Consigue la Tarjeta de Expansión de Radio aquí para despertar a Snorlax.",
  "Isla Canela / Espuma": "Blaine está en las Islas Espuma. Atrapa a Articuno en el nivel más bajo.",
  "Monte Plateado": "La prueba final. Prepárate para el granizo perpetuo contra Red."
};
const LOCATIONS = Object.keys(LOCATION_TRIVIA);

// --- BASE DE DATOS DE LEYENDAS Y COMPETITIVO ---
const LEGENDARIES = [
  { id: 'lugia', name: 'Lugia', type: 'Psíquico / Volador', tip: 'Islas Remolino. Ocupas Ala Plateada y Cascada/Torbellino. Nv. 45 (SS) o Nv. 70 (HG).', prep: 'Ocaso Balls o Peso Balls. Usa Mal de Ojo y Falsotortazo con Gallade.', comp: 'Muralla inquebrantable. Úsalo con Respiro, Tóxico, Aerochorro y Remolino para agotar al rival.' },
  { id: 'hooh', name: 'Ho-Oh', type: 'Fuego / Volador', tip: 'Torre Campana. Ocupas Ala Arcoíris. Nv. 45 (HG) o Nv. 70 (SS).', prep: 'Ultra Balls de día o Ocaso de Noche. Paralízalo (Onda Trueno), ¡no lo quemes ni envenenes!', comp: 'Atacante físico brutal. Fuego Sagrado, Pájaro Osado, Terremoto y Respiro.' },
  { id: 'raikou', name: 'Raikou', type: 'Eléctrico', tip: 'Errante en Johto. Nv. 40. Huye al primer turno y usa Rugido.', prep: 'Gengar con Mal de Ojo o Smeargle con Esporas/Telaraña. Usa Rapid Balls o Turno Balls.', comp: 'Sweeper Especial veloz. Paz Mental, Rayo, Bola Sombra y Poder Oculto Hielo/Planta.' },
  { id: 'entei', name: 'Entei', type: 'Fuego', tip: 'Errante en Johto. Nv. 40. Huye al primer turno y usa Rugido.', prep: 'Misma estrategia que Raikou. Duérmelo para evitar que use Rugido.', comp: 'Atacante físico. Envite Ígneo, Velocidad Extrema, Roca Afilada y Triturar.' },
  { id: 'suicune', name: 'Suicune', type: 'Agua', tip: 'Ruta 25 (Cabo Celeste, Kanto). Te espera estático al Nv. 40 tras perseguirlo.', prep: 'Malla Balls o Red Balls (pesan mucho). Falsotortazo y Esporas.', comp: 'Tanque puro (Crocune). Paz Mental, Surf/Escaldar, Descanso y Sonámbulo.' },
  { id: 'zapdos', name: 'Zapdos', type: 'Eléctrico / Volador', tip: 'Ruta 10 (Central Energía, Kanto). Nv. 50.', prep: 'Resiste Falsotortazo. Usa Ocaso Balls si es de noche, sino Ultra Balls.', comp: 'Pivote ofensivo. Rayo, Onda Ígnea, Respiro y Voltiocambio/Ida y Vuelta.' },
  { id: 'articuno', name: 'Articuno', type: 'Hielo / Volador', tip: 'Islas Espuma (Kanto). Requiere Fuerza y Surf. Nv. 50.', prep: 'Ocaso Balls (es cueva). Cuidado, usa ataques que pueden congelar.', comp: 'Sufre mucho por Trampa Rocas. Úsalo con Rayo Hielo, Respiro y Viento Afín/Tóxico.' },
  { id: 'moltres', name: 'Moltres', type: 'Fuego / Volador', tip: 'Monte Plateado (Cámara Inferior). Requiere Treparrocas y Surf. Nv. 50.', prep: 'Ocaso Balls. Resiste Falsotortazo, lleva un Pokémon de Agua defensivo.', comp: 'Fuerte atacante especial. Lanzallamas, Tajo Aéreo, Respiro y Fuego Fatuo.' },
  { id: 'mewtwo', name: 'Mewtwo', type: 'Psíquico', tip: 'Cueva Celeste (Kanto). Requiere 16 Medallas. Nv. 70.', prep: 'Ocaso Balls. Cuidado con Psico-Corte y Amnesia. Un Pokémon Siniestro anula sus ataques STAB.', comp: 'Rey del daño Especial. Onda Certera, Bola Sombra, Psíquico/Onda Mental y Rayo Hielo.' },
  { id: 'lati', name: 'Latias / Latios', type: 'Dragón / Psíquico', tip: 'Errante en Kanto tras recuperar el muñeco de Copiona y hablar con Máximo en Ciudad Carmín.', prep: 'Turno Balls y Rapid Balls. Usa un Pokémon con Mal de Ojo veloz.', comp: 'Latios: Atacante (Gafas Elegid., Cometa Draco, Surf). Latias: Apoyo/Defensa.' },
  { id: 'kyogre_groudon', name: 'Kyogre / Groudon', type: 'Agua / Tierra', tip: 'Torre Oculta (Ruta 47). Requiere inicial de Kanto con Oak y Orbe de Sr. Pokémon. Nv. 50.', prep: 'Peso Balls (¡son muy pesados, la captura será más fácil!).', comp: 'Groudon: Terremoto bajo sol. Kyogre: Salpicar y Trueno bajo lluvia.' },
  { id: 'rayquaza', name: 'Rayquaza', type: 'Dragón / Volador', tip: 'Torre Oculta. ¡Debes tener a Kyogre y Groudon de ambas versiones en tu partida!', prep: 'Ocaso Balls. Cuidado con Enfado y Descanso (se cura a sí mismo).', comp: 'Rompe muros. Danza Dragón, Enfado, Velocidad Extrema y Terremoto.' }
];

// --- BASE DE DATOS DE FRENTE BATALLA Y ENTRENAMIENTO ---
const BATTLE_FRONTIER_SHOP = [
  { item: "Power Bracer / Belt / Lens / etc.", cost: "16 BP", desc: "Reduce la Velocidad en combate pero aumenta el crecimiento de EVs (+4) del stat correspondiente por cada Pokémon derrotado." },
  { item: "Toxic Orb / Flame Orb", cost: "16 BP", desc: "Envenena/Quema al portador. Útil para activar habilidades como Agallas o Pies Rápidos." },
  { item: "Hierba Blanca (White Herb)", cost: "32 BP", desc: "Cura las bajadas de estadísticas. Se consume tras usarse." },
  { item: "Cinta Elegida (Choice Band)", cost: "48 BP", desc: "Aumenta el Ataque Físico 50%, pero te encierra en el primer movimiento." },
  { item: "Gafas Elegidas (Choice Specs)", cost: "48 BP", desc: "Aumenta el Ataque Especial 50%, pero te encierra en el primer movimiento." },
  { item: "Pañuelo Elegido (Choice Scarf)", cost: "48 BP", desc: "Aumenta la Velocidad 50%, pero te encierra en el primer movimiento." },
  { item: "Banda Focus (Focus Sash)", cost: "48 BP", desc: "Te permite sobrevivir a 1 PS. ATENCIÓN: Solo es infinita en el Frente Batalla. En aventura SE DESTRUYE." }
];

const NATURES_GUIDE = [
  { nature: "Firme (Adamant)", up: "Ataque", down: "Atq. Especial", ideal: "Feraligatr, Gyarados, Scizor" },
  { nature: "Modesta (Modest)", up: "Atq. Especial", down: "Ataque", ideal: "Gengar, Alakazam, Typhlosion" },
  { nature: "Alegre (Jolly)", up: "Velocidad", down: "Atq. Especial", ideal: "Garchomp, Aerodactyl" },
  { nature: "Miedosa (Timid)", up: "Velocidad", down: "Ataque", ideal: "Starmie, Raikou" },
  { nature: "Osada (Bold)", up: "Defensa", down: "Ataque", ideal: "Suicune, Lugia" },
  { nature: "Cauta (Careful)", up: "Def. Especial", down: "Atq. Especial", ideal: "Umbreon, Snorlax" }
];

// --- DATOS PARA CALCULADORA DE EQUIPOS ---
const POKEMON_TYPES = {
  "Feraligatr": ["Agua"], "Haunter": ["Fantasma", "Veneno"], "Gengar": ["Fantasma", "Veneno"],
  "Arcanine": ["Fuego"], "Ampharos": ["Eléctrico"], "Togetic": ["Normal", "Volador"], "Togekiss": ["Normal", "Volador"],
  "Kadabra": ["Psíquico"], "Alakazam": ["Psíquico"], "Gyarados": ["Agua", "Volador"],
  "Weepinbell": ["Planta", "Veneno"], "Victreebel": ["Planta", "Veneno"], "Onix": ["Roca", "Tierra"], "Steelix": ["Acero", "Tierra"],
  "Lugia": ["Psíquico", "Volador"], "Ho-Oh": ["Fuego", "Volador"], "Raikou": ["Eléctrico"], "Entei": ["Fuego"],
  "Suicune": ["Agua"], "Zapdos": ["Eléctrico", "Volador"], "Articuno": ["Hielo", "Volador"], "Moltres": ["Fuego", "Volador"],
  "Mewtwo": ["Psíquico"], "Latias": ["Dragón", "Psíquico"], "Latios": ["Dragón", "Psíquico"],
  "Kyogre": ["Agua"], "Groudon": ["Tierra"], "Rayquaza": ["Dragón", "Volador"]
};

// Qué tipos atacan Súper Efectivo a ESTOS TIPOS (Defensa)
const TYPE_WEAKNESSES = {
  "Normal": ["Lucha"], "Fuego": ["Agua", "Tierra", "Roca"], "Agua": ["Planta", "Eléctrico"],
  "Planta": ["Fuego", "Hielo", "Veneno", "Volador", "Bicho"], "Eléctrico": ["Tierra"],
  "Hielo": ["Fuego", "Lucha", "Roca", "Acero"], "Lucha": ["Volador", "Psíquico"],
  "Veneno": ["Tierra", "Psíquico"], "Tierra": ["Agua", "Planta", "Hielo"],
  "Volador": ["Eléctrico", "Hielo", "Roca"], "Psíquico": ["Bicho", "Fantasma", "Siniestro"],
  "Bicho": ["Fuego", "Volador", "Roca"], "Roca": ["Agua", "Planta", "Lucha", "Tierra", "Acero"],
  "Fantasma": ["Fantasma", "Siniestro"], "Dragón": ["Hielo", "Dragón"],
  "Siniestro": ["Lucha", "Bicho"], "Acero": ["Fuego", "Lucha", "Tierra"]
};

// Qué tipos son vulnerables a ESTOS ATAQUES (Ofensiva)
const TYPE_OFFENSE = {
  "Agua": ["Fuego", "Tierra", "Roca"], "Fuego": ["Planta", "Hielo", "Bicho", "Acero"],
  "Planta": ["Agua", "Tierra", "Roca"], "Eléctrico": ["Agua", "Volador"],
  "Hielo": ["Planta", "Tierra", "Volador", "Dragón"], "Lucha": ["Normal", "Hielo", "Roca", "Siniestro", "Acero"],
  "Veneno": ["Planta"], "Tierra": ["Fuego", "Eléctrico", "Veneno", "Roca", "Acero"],
  "Volador": ["Planta", "Lucha", "Bicho"], "Psíquico": ["Lucha", "Veneno"],
  "Bicho": ["Planta", "Psíquico", "Siniestro"], "Roca": ["Fuego", "Hielo", "Volador", "Bicho"],
  "Fantasma": ["Psíquico", "Fantasma"], "Dragón": ["Dragón"],
  "Siniestro": ["Psíquico", "Fantasma"], "Acero": ["Hielo", "Roca"], "Normal": []
};

// --- BASE DE DATOS DE MOVIMIENTOS Y AUDITORÍA ---
const MOVES_DB = {
  "Cascada": { loc: "MO07 - Cueva Helada.", unique: false, type: "Agua" },
  "Colm. Hielo": { loc: "Recordador (Escama Corazón) o Nivel 21.", unique: false, type: "Hielo" },
  "Triturar": { loc: "Aprendido por Nivel.", unique: false, type: "Siniestro" },
  "Danza Espada": { loc: "MT75 - Casino Trigal (10k Fichas).", unique: false, type: "Normal" },
  "Terremoto": { loc: "MT26 - Calle Victoria o Frente Batalla (80 PB).", unique: true, type: "Tierra" },
  "Bola Sombra": { loc: "MT30 - Gimnasio Iris (Morti) o Frente Batalla (80 PB).", unique: true, type: "Fantasma" },
  "Bomba Lodo": { loc: "MT36 - Ruta 43 (Caseta de Peaje).", unique: true, type: "Veneno" },
  "Onda Certera": { loc: "MT52 - C. Comercial Trigal (5,500 Pokedólares).", unique: false, type: "Lucha" },
  "Rayo": { loc: "MT24 - Casino Trigal (10k Fichas) o Cueva Celeste.", unique: false, type: "Eléctrico" },
  "Psíquico": { loc: "MT29 - Ciudad Azafrán (Sr. Psíquico) o Nivel.", unique: true, type: "Psíquico" },
  "Lanzallamas": { loc: "MT35 - Casino Trigal o Ruta 28.", unique: false, type: "Fuego" },
  "Velocidad Extrema": { loc: "Aprendido por Nivel 39 (Arcanine).", unique: false, type: "Normal" },
  "Chispazo": { loc: "Aprendido por Nivel 34 (Ampharos).", unique: false, type: "Eléctrico" },
  "Joya de Luz": { loc: "Recordador de Movimientos (Escama Corazón).", unique: false, type: "Roca" },
  "Onda Trueno": { loc: "MT73 - Frente Batalla o por Nivel.", unique: false, type: "Eléctrico" },
  "Respiro": { loc: "MT51 - Gimnasio Malva (Pegaso).", unique: true, type: "Volador" },
  "Paz Mental": { loc: "MT04 - Frente Batalla (48 PB).", unique: false, type: "Psíquico" },
  "Corte": { loc: "MO01 - Cueva del Encinar.", unique: false, type: "Normal" },
  "Fuerza": { loc: "MO04 - Ruta 42.", unique: false, type: "Normal" },
  "Surf": { loc: "MO03 - Teatro Danza (Ciudad Iris).", unique: false, type: "Agua" },
  "Vuelo": { loc: "MO02 - Ciudad Orquídea (Tras vencer Chuck).", unique: false, type: "Volador" },
  "Hoja Afilada": { loc: "Aprendido por Nivel.", unique: false, type: "Planta" },
  "Dulce Aroma": { loc: "Aprendido por Nivel.", unique: false, type: "Normal" },
  "Rayo Confuso": { loc: "Aprendido por Nivel.", unique: false, type: "Fantasma" },
  "Psicorrayo": { loc: "Aprendido por Nivel.", unique: false, type: "Psíquico" },
  "Recuperación": { loc: "Aprendido por Nivel.", unique: false, type: "Normal" }
};

// Función para obtener tipos de movimientos adicionales (Calculadora Ofensiva)
const getMoveType = (move) => {
  if (MOVES_DB[move]?.type) return MOVES_DB[move].type;
  const extraTypes = {
      "Acua Cola": "Agua", "Mismo Destino": "Fantasma", "Pulso Dragón": "Dragón",
      "Colm. Igneo": "Fuego", "Rueda Fuego": "Fuego", "Placaje": "Normal", "Carga": "Eléctrico",
      "Puño Trueno": "Eléctrico", "Poder Pasado": "Roca", "Paranormal": "Psíquico",
      "Daño Secreto": "Normal", "Ciclón": "Dragón", "Torbellino": "Agua",
      "Polvo Veneno": "Veneno", "Ácido": "Veneno", "Lanzarrocas": "Roca",
      "Tumba Rocas": "Roca", "Golpe Roca": "Lucha", "Sofoco": "Fuego",
      "Doble Rayo": "Bicho", "Pantalla de Luz": "Psíquico", "Esfera Aural": "Lucha",
      "Deseo": "Normal", "Tajo Aéreo": "Volador", "Reflejo": "Psíquico",
      "Danza Dragón": "Dragón", "Somnífero": "Planta", "Cola Férrea": "Acero",
      "Lluevehojas": "Planta", "Giro Bola": "Acero", "Trampa Rocas": "Roca",
      "Protección": "Normal", "Sustituto": "Normal", "Tóxico": "Veneno", "Retribución": "Normal",
      "Aerochorro": "Volador", "Fuego Sagrado": "Fuego", "Roca Afilada": "Roca", 
      "Envite Ígneo": "Fuego", "Escaldar": "Agua", "Descanso": "Normal", "Sonámbulo": "Normal",
      "Onda Ígnea": "Fuego", "Voltiocambio": "Eléctrico", "Ida y Vuelta": "Bicho",
      "Viento Afín": "Volador", "Fuego Fatuo": "Fuego", "Onda Mental": "Psíquico",
      "Cometa Draco": "Dragón", "Salpicar": "Agua", "Trueno": "Eléctrico",
      "Puño Fuego": "Fuego", "Enfado": "Dragón"
  };
  return extraTypes[move] || "Normal";
};

const WEAK_MOVES = {
  "Placaje": "Ataque obsoleto. Reemplázalo urgentemente por un ataque con STAB o Fuerza.",
  "Confusión": "Muy débil. Evoluciona a Psíquico o Psicorrayo.",
  "Torbellino": "Baja potencia. En combate usa Surf/Cascada. Úsalo solo para explorar.",
  "Impactrueno": "Daño bajo. Busca Chispazo o Rayo.",
  "Ácido": "Muy baja potencia. Sustituye por Bomba Lodo.",
  "Rueda Fuego": "Buen ataque temprano, pero a Nv. 37+ necesitas Lanzallamas o Colm. Ígneo.",
  "Lanzarrocas": "Baja precisión y daño. Terremoto o Fuerza son mejores."
};

// --- SETS PERFECTOS COMPLETOS ---
const PERFECT_SETS = {
  "Feraligatr": ["Cascada", "Colm. Hielo", "Triturar", "Danza Espada"],
  "Gengar": ["Bola Sombra", "Bomba Lodo", "Rayo", "Onda Certera"],
  "Haunter": ["Bola Sombra", "Bomba Lodo", "Rayo Confuso", "Psíquico"],
  "Arcanine": ["Lanzallamas", "Velocidad Extrema", "Colm. Igneo", "Triturar"],
  "Ampharos": ["Rayo", "Chispazo", "Joya de Luz", "Onda Trueno"],
  "Togekiss": ["Tajo Aéreo", "Esfera Aural", "Respiro", "Lanzallamas"],
  "Togetic": ["Respiro", "Paranormal", "Vuelo", "Poder Pasado"],
  "Alakazam": ["Psíquico", "Onda Certera", "Bola Sombra", "Paz Mental"],
  "Kadabra": ["Psíquico", "Psicorrayo", "Bola Sombra", "Recuperación"],
  "Gyarados": ["Cascada", "Danza Dragón", "Colm. Hielo", "Terremoto"],
  "Victreebel": ["Lluevehojas", "Bomba Lodo", "Hoja Afilada", "Danza Espada"],
  "Steelix": ["Terremoto", "Giro Bola", "Trampa Rocas", "Triturar"],
  "Lugia": ["Respiro", "Tóxico", "Aerochorro", "Terremoto"],
  "Ho-Oh": ["Fuego Sagrado", "Terremoto", "Respiro", "Pájaro Osado"],
  "Raikou": ["Rayo", "Paz Mental", "Bola Sombra", "Protección"],
  "Entei": ["Envite Ígneo", "Velocidad Extrema", "Roca Afilada", "Triturar"],
  "Suicune": ["Surf", "Paz Mental", "Rayo Hielo", "Descanso"],
  "Zapdos": ["Rayo", "Onda Ígnea", "Respiro", "Voltiocambio"],
  "Articuno": ["Rayo Hielo", "Respiro", "Viento Afín", "Tóxico"],
  "Moltres": ["Lanzallamas", "Tajo Aéreo", "Respiro", "Fuego Fatuo"],
  "Mewtwo": ["Psíquico", "Onda Certera", "Bola Sombra", "Rayo Hielo"],
  "Latias": ["Pulso Dragón", "Psíquico", "Recuperación", "Paz Mental"],
  "Latios": ["Cometa Draco", "Psíquico", "Surf", "Rayo"],
  "Kyogre": ["Salpicar", "Trueno", "Rayo Hielo", "Paz Mental"],
  "Groudon": ["Terremoto", "Puño Fuego", "Roca Afilada", "Danza Espada"],
  "Rayquaza": ["Danza Dragón", "Enfado", "Velocidad Extrema", "Terremoto"]
};

// Sugerencias para el Selector (Equipo Aventura)
const RECOMMENDED_MOVES = {
  "Feraligatr": ["Cascada", "Colm. Hielo", "Triturar", "Danza Espada", "Terremoto", "Surf", "Acua Cola"],
  "Haunter": ["Bola Sombra", "Bomba Lodo", "Rayo Confuso", "Psíquico", "Mismo Destino", "Onda Certera"],
  "Gengar": ["Bola Sombra", "Bomba Lodo", "Rayo", "Onda Certera", "Psíquico", "Mismo Destino"],
  "Arcanine": ["Lanzallamas", "Velocidad Extrema", "Colm. Igneo", "Pulso Dragón", "Triturar", "Sofoco"],
  "Ampharos": ["Rayo", "Chispazo", "Joya de Luz", "Onda Trueno", "Doble Rayo", "Pantalla de Luz"],
  "Togetic": ["Respiro", "Paranormal", "Vuelo", "Poder Pasado", "Esfera Aural", "Deseo"],
  "Togekiss": ["Tajo Aéreo", "Esfera Aural", "Respiro", "Lanzallamas", "Onda Trueno"],
  "Kadabra": ["Psíquico", "Psicorrayo", "Bola Sombra", "Recuperación", "Reflejo"],
  "Alakazam": ["Psíquico", "Onda Certera", "Bola Sombra", "Paz Mental", "Recuperación"],
  "Gyarados": ["Cascada", "Danza Dragón", "Colm. Hielo", "Terremoto", "Ciclón", "Torbellino"],
  "Weepinbell": ["Bomba Lodo", "Hoja Afilada", "Somnífero", "Corte", "Dulce Aroma", "Polvo Veneno"],
  "Onix": ["Terremoto", "Trampa Rocas", "Lanzarrocas", "Fuerza", "Golpe Roca", "Cola Férrea"]
};

const GENERIC_MOVES = ["Protección", "Sustituto", "Tóxico", "Terremoto", "Retribución", "Corte", "Fuerza", "Golpe Roca"];

// --- GUÍA DE EVOLUCIÓN ---
const EVOLUTIONS = {
  "Haunter": "Gengar", "Kadabra": "Alakazam", "Graveler": "Golem", 
  "Togetic": "Togekiss", "Weepinbell": "Victreebel", "Onix": "Steelix"
};

const EVOLUTION_METHODS = {
  "Gengar": "Intercambio (Usa 2 instancias de MelonDS sin límite de frames para intercambiar contigo mismo).",
  "Alakazam": "Intercambio (Usa 2 instancias de MelonDS sin límite de frames).",
  "Golem": "Intercambio (Usa 2 instancias de MelonDS sin límite de frames).",
  "Togekiss": "Usando la Piedra Día (Parque Nacional - Post-Liga o Pokéathlon).",
  "Victreebel": "Usando la Piedra Hoja (Ruta 34 o Pokéathlon).",
  "Steelix": "Intercambio equipado con Revestimiento Metálico (MelonDS)."
};

// --- BASE DE DATOS DE OBJETOS ---
const COMMON_ITEMS = [
  "Ninguno", "Restos", "Vidasfera", "Gafas Elegid.", "Cinta Elegid.", "Pañuelo Elegid.", 
  "Banda Focus", "Campana Alivio", "Repartir Exp", "Imán", "Carbón", "Agua Mística", "Cuchara Torcida"
];

const ITEMS_INFO = {
  "Restos": { loc: "Cubo de Basura (Restaurante Ciudad Azulona) o Snorlax salvajes.", warn: "Cura 1/16 de PS máximo cada turno. El mejor objeto para aguantar." },
  "Vidasfera": { loc: "Ruinas Alfa (necesitas Surf y Treparrocas para la sala exterior).", warn: "⚠️ Sube daño 30%, pero PIERDES 10% de PS al atacar. Para Pokémon rápidos." },
  "Gafas Elegid.": { loc: "Lago de la Furia (Noreste, solo cuando está inundado).", warn: "⚠️ Aumenta Ataque Especial 50%, pero TE BLOQUEA a usar un solo movimiento." },
  "Cinta Elegid.": { loc: "Frente Batalla (48 Puntos de Batalla).", warn: "⚠️ Aumenta Ataque Físico 50%, pero TE BLOQUEA a usar un solo movimiento." },
  "Pañuelo Elegid.": { loc: "Ahorros de tu mamá o Frente Batalla (48 PB).", warn: "⚠️ Aumenta Velocidad 50%, pero TE BLOQUEA a usar un solo movimiento." },
  "Banda Focus": { loc: "Dojo Kárate (Saffron City) o Frente Batalla.", warn: "🚨 ¡CUIDADO! Te deja a 1 PS, pero SE DESTRUYE para siempre si se usa en la aventura. Solo sirve infinito en Torre Batalla." },
  "Campana Alivio": { loc: "Parque Nacional (zona norte, oculta tras la valla).", warn: "Aumenta la felicidad obtenida. Útil para evolucionar a Togepi." },
  "Repartir Exp": { loc: "Ruta 30: Entrégale la Escama Roja al Sr. Pokémon.", warn: "Gana 50% de la experiencia sin pelear. Ideal para Obreros MO." },
  "Imán": { loc: "Ruta 37 (Te lo da Dominga los días Domingo).", warn: "Sube daño de ataques Eléctricos un 20%. Genial para Ampharos." },
  "Carbón": { loc: "Pueblo Azalea (Casa del aprendiz tras salvar a los Farfetch'd).", warn: "Sube daño de ataques Fuego un 20%. Genial para Arcanine." },
  "Agua Mística": { loc: "Ciudad Cerezo (Usando Surf hacia la isla al sur).", warn: "Sube daño de ataques Agua un 20%. Perfecto para Feraligatr." },
  "Cuchara Torcida": { loc: "Equipada en Abra salvajes (5%).", warn: "Sube daño de ataques Psíquicos un 20%. Bueno para Alakazam." }
};

// --- DATOS INICIALES ---
const defaultData = {
  trainer: { medallas: 8, ubicacion: "Ciudad Endrino", eventoActual: 'liga_pokemon', leyendasCapturadas: {} },
  mainTeam: [
    { id: 1, mote: "Aguatero", especie: "Feraligatr", nivel: 39, objeto: "Agua Mística", movimientos: ["Colm. Hielo", "Triturar", "Cuchillada", "Surf"] },
    { id: 2, mote: "Gaseoso", especie: "Haunter", nivel: 38, objeto: "Ninguno", movimientos: ["Bola Sombra", "Garra Umbria", "Rayo Confuso", "Bomba Lodo"] },
    { id: 3, mote: "Perro", especie: "Arcanine", nivel: 37, objeto: "Carbón", movimientos: ["Lanzallamas", "Pulso Dragón", "Colm. Igneo", "Rueda Fuego"] },
    { id: 4, mote: "Oveja", especie: "Ampharos", nivel: 36, objeto: "Imán", movimientos: ["Placaje", "Carga", "Chispazo", "Puño Trueno"] },
    { id: 5, mote: "Huevo", especie: "Togetic", nivel: 35, objeto: "Campana Alivio", movimientos: ["Respiro", "Poder Pasado", "Paranormal", "Vuelo"] },
    { id: 6, mote: "Mago", especie: "Kadabra", nivel: 32, objeto: "Cuchara Torcida", movimientos: ["Psicorrayo", "Confusión", "Daño Secreto", "Recuperación"] }
  ],
  hmTeam: [
    { id: 7, mote: "Dragón", especie: "Gyarados", nivel: 35, objeto: "Repartir Exp", movimientos: ["Ciclón", "Colm. Hielo", "Acua Cola", "Torbellino"] },
    { id: 8, mote: "Matica", especie: "Weepinbell", nivel: 33, objeto: "Ninguno", movimientos: ["Polvo Veneno", "Ácido", "Corte", "Dulce Aroma"] },
    { id: 9, mote: "Temblores", especie: "Onix", nivel: 24, objeto: "Ninguno", movimientos: ["Lanzarrocas", "Tumba Rocas", "Golpe Roca", "Fuerza"] }
  ]
};

const getSpriteUrl = (especie) => `https://play.pokemonshowdown.com/sprites/gen4/${especie.toLowerCase().replace(/[^a-z0-9]/g, '')}.png`;

// --- COMPONENTE PRINCIPAL ---
export default function App() {
  const [data, setData] = useState(() => {
    try {
      const savedData = localStorage.getItem('bubloy-tracker-v7');
      if (savedData) {
        const parsed = JSON.parse(savedData);
        const hmTeamSafe = (parsed.hmTeam && parsed.hmTeam.some(p => p.especie === 'Weepinbell')) ? parsed.hmTeam : defaultData.hmTeam;
        return { ...defaultData, ...parsed, hmTeam: hmTeamSafe, trainer: { ...defaultData.trainer, ...parsed.trainer } };
      }
      return defaultData;
    } catch (e) { return defaultData; }
  });

  const [editingPokemon, setEditingPokemon] = useState(null);
  const [activeTab, setActiveTab] = useState('main'); // main, hm, legendaries, frontier, calc
  const [showDataModal, setShowDataModal] = useState(false);
  const [importText, setImportText] = useState("");
  const [importError, setImportError] = useState("");
  
  const [calcTeam, setCalcTeam] = useState([]);

  useEffect(() => { localStorage.setItem('bubloy-tracker-v7', JSON.stringify(data)); }, [data]);

  // Exportar/Importar Lógica
  const copyToClipboard = () => {
    navigator.clipboard.writeText(JSON.stringify(data)).then(() => alert("¡Datos de guardado copiados!"));
  };

  const handleImport = () => {
    try {
      if(!importText) return;
      const parsed = JSON.parse(importText);
      if(parsed.trainer && parsed.mainTeam) {
        setData(parsed);
        setShowDataModal(false);
        setImportText("");
        setImportError("");
        alert("¡Partida restaurada con éxito!");
      } else { setImportError("El formato JSON no es válido para este Tracker."); }
    } catch(err) { setImportError("Error al leer los datos. Asegúrate de copiar todo el texto."); }
  };

  const getAllOwnedTMs = () => {
    const allMoves = [...data.mainTeam, ...data.hmTeam].flatMap(p => p.movimientos);
    const tmCounts = {};
    allMoves.forEach(m => {
      if (m && MOVES_DB[m] && MOVES_DB[m].unique) tmCounts[m] = (tmCounts[m] || 0) + 1;
    });
    return tmCounts;
  };
  const globalTMCounts = getAllOwnedTMs();

  const getPokemonAlerts = (p, isMainTeam) => {
    const alerts = [];
    if (EVOLUTIONS[p.especie]) {
      const isTrade = ['Haunter', 'Kadabra', 'Onix'].includes(p.especie);
      alerts.push({ type: 'evo', text: `Puede evolucionar a ${EVOLUTIONS[p.especie]} por ${isTrade ? 'Intercambio' : 'Objeto'}.` });
    }
    p.movimientos.forEach(mov => {
      if (isMainTeam && p.nivel >= 30 && WEAK_MOVES[mov]) alerts.push({ type: 'warn', text: `Ataque Débil [${mov}]: ${WEAK_MOVES[mov]}` });
      if (globalTMCounts[mov] > 1 && MOVES_DB[mov]?.unique) alerts.push({ type: 'danger', text: `¡CONFLICTO! La MT [${mov}] es única y ya se asignó a otro Pokémon.` });
    });
    if (isMainTeam && p.especie === 'Feraligatr' && p.movimientos.includes('Surf')) alerts.push({ type: 'warn', text: 'Para la Liga, cambia Surf por Cascada. Feraligatr es físico.' });
    if (isMainTeam && p.especie === 'Haunter' && p.movimientos.includes('Garra Umbria')) alerts.push({ type: 'warn', text: 'Quita Garra Umbría (Físico). Usa ataques Especiales.' });
    if (p.objeto === 'Banda Focus') alerts.push({ type: 'danger', text: '🚨 ¡Quita la Banda Focus! Se destruirá permanentemente si peleas fuera de Torre Batalla.' });
    if (isMainTeam && ['Gengar', 'Alakazam'].includes(p.especie) && (!p.objeto || p.objeto === 'Ninguno')) alerts.push({ type: 'tip', text: `Equipa Gafas Elegid., Cuchara Torcida o Vidasfera para destruir.` });
    if (isMainTeam && ['Feraligatr', 'Arcanine'].includes(p.especie) && (!p.objeto || p.objeto === 'Ninguno')) alerts.push({ type: 'tip', text: `Aumenta su poder físico con Vidasfera, Carbón o Agua Mística.` });
    return alerts;
  };

  const currentEventData = GAME_EVENTS.find(e => e.id === data.trainer.eventoActual) || GAME_EVENTS[3];

  const handleEditClick = (pokemon, isMain) => setEditingPokemon({ ...pokemon, isMain });

  const handleSave = () => {
    const listKey = editingPokemon.isMain ? 'mainTeam' : 'hmTeam';
    const updatedList = data[listKey].map(p => p.id === editingPokemon.id ? editingPokemon : p);
    setData({ ...data, [listKey]: updatedList });
    
    // Actualizar también en el equipo de la calculadora si está ahí
    setCalcTeam(calcTeam.map(p => p.id === editingPokemon.id ? editingPokemon : p));
    setEditingPokemon(null);
  };

  const handleEvolve = () => {
    if (EVOLUTIONS[editingPokemon.especie]) {
      setEditingPokemon({ ...editingPokemon, especie: EVOLUTIONS[editingPokemon.especie] });
    }
  };

  const updateMove = (index, value) => {
    const newMoves = [...editingPokemon.movimientos];
    newMoves[index] = value;
    setEditingPokemon({ ...editingPokemon, movimientos: newMoves });
  };

  const handleToggleLegendary = (id) => {
    setData(prev => ({ ...prev, trainer: { ...prev.trainer, leyendasCapturadas: { ...prev.trainer.leyendasCapturadas, [id]: !prev.trainer.leyendasCapturadas[id] } } }));
  };

  // --- LÓGICA CALCULADORA DE EQUIPOS ---
  const availablePool = [
    ...data.mainTeam, 
    ...data.hmTeam, 
    ...LEGENDARIES.filter(l => data.trainer.leyendasCapturadas[l.id]).map(l => ({
      id: `leg_${l.id}`, mote: l.name, especie: l.name, nivel: 50, objeto: "Ninguno", 
      movimientos: PERFECT_SETS[l.name] || ["Protección", "Tóxico", "Terremoto", "Retribución"]
    }))
  ];

  const toggleCalcMember = (pokemon) => {
    if (calcTeam.some(p => p.id === pokemon.id)) {
      setCalcTeam(calcTeam.filter(p => p.id !== pokemon.id));
    } else if (calcTeam.length < 6) {
      setCalcTeam([...calcTeam, pokemon]);
    }
  };

  const analyzeTeam = () => {
    let weaknesses = {};
    let coverage = new Set();

    calcTeam.forEach(p => {
      const types = POKEMON_TYPES[p.especie] || ["Normal"];
      types.forEach(t => {
        (TYPE_WEAKNESSES[t] || []).forEach(weakTo => weaknesses[weakTo] = (weaknesses[weakTo] || 0) + 1);
      });

      p.movimientos.forEach(m => {
        if (m && m !== "-") {
          const moveType = getMoveType(m);
          (TYPE_OFFENSE[moveType] || []).forEach(hits => coverage.add(hits));
        }
      });
    });

    const criticalWeaknesses = Object.entries(weaknesses).filter(([_, count]) => count >= 3);
    const missingCoverage = Object.keys(TYPE_WEAKNESSES).filter(t => !coverage.has(t) && t !== "Normal");
    return { criticalWeaknesses, missingCoverage, coverageSize: coverage.size };
  };
  const analysis = analyzeTeam();

  const PokemonCard = ({ pokemon, isMain }) => {
    const alerts = getPokemonAlerts(pokemon, isMain);
    return (
      <div className="bg-white rounded-xl shadow-md border-2 border-yellow-200 overflow-hidden flex flex-col h-full hover:shadow-xl transition-all select-none">
        <div className="bg-gradient-to-r from-red-600 to-red-500 p-3 flex justify-between items-center text-white">
          <div>
            <h3 className="font-bold text-lg flex items-center gap-2">
              {pokemon.mote} <span className="text-xs bg-black/30 px-2 py-1 rounded-full shadow-inner">Lv. {pokemon.nivel}</span>
            </h3>
            <p className="text-red-100 text-sm font-medium">{pokemon.especie} {isMain ? "" : "(Apoyo MO)"}</p>
          </div>
          <button onClick={() => handleEditClick(pokemon, isMain)} className="p-2.5 bg-white/20 rounded-full hover:bg-white/40 active:bg-white/50 transition-colors shadow-sm">
            <Edit2 size={18} />
          </button>
        </div>
        
        <div className="p-4 pt-8 relative flex flex-col grow">
          <div className="absolute -top-10 left-1/2 transform -translate-x-1/2">
            <div className="bg-yellow-50 rounded-full p-1 border-4 border-white shadow-md w-16 h-16 flex items-center justify-center">
              <img src={getSpriteUrl(pokemon.especie)} onError={(e) => e.target.src = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/items/poke-ball.png"} alt={pokemon.especie} className="max-w-full max-h-full object-contain" style={{ imageRendering: 'pixelated' }} />
            </div>
          </div>

          <div className="flex items-center justify-center gap-1 text-xs text-gray-500 font-semibold mb-3 bg-gray-50 py-1 rounded-md border border-gray-100">
            <Briefcase size={12} className="text-blue-500 shrink-0" />
            <span className="truncate">Objeto: {pokemon.objeto || "Ninguno"}</span>
          </div>

          <div className="grid grid-cols-2 gap-2 mb-4">
            {pokemon.movimientos.map((mov, idx) => {
              const isDupe = globalTMCounts[mov] > 1 && MOVES_DB[mov]?.unique;
              const isWeak = WEAK_MOVES[mov] && pokemon.nivel >= 30 && isMain;
              return (
                <div key={idx} className={`p-1.5 rounded text-[11px] font-bold text-center border truncate shadow-sm ${isWeak || isDupe ? 'bg-red-100 border-red-300 text-red-800' : 'bg-gray-100 border-gray-200 text-gray-700'}`}>
                  {mov === "-" || !mov ? "Vacío" : mov}
                </div>
              )
            })}
          </div>

          <div className="mt-auto space-y-1.5 pt-2 border-t border-gray-100">
            {alerts.length > 0 ? (
              alerts.map((alert, idx) => (
                <div key={idx} className={`p-1.5 flex gap-1.5 items-start rounded text-[11px] font-medium leading-tight ${alert.type === 'warn' || alert.type === 'danger' ? 'bg-red-50 text-red-700' : alert.type === 'evo' ? 'bg-purple-50 text-purple-700' : 'bg-blue-50 text-blue-700'}`}>
                  {alert.type === 'warn' || alert.type === 'danger' ? <AlertCircle size={12} className="shrink-0 mt-0.5" /> : alert.type === 'evo' ? <ArrowUpCircle size={12} className="shrink-0 mt-0.5" /> : <Info size={12} className="shrink-0 mt-0.5" />}
                  <span>{alert.text}</span>
                </div>
              ))
            ) : (
              <div className="p-1.5 flex gap-1.5 items-center justify-center rounded text-[11px] font-bold bg-green-50 text-green-700">
                <CheckCircle2 size={12} /> {isMain ? "Set Óptimo Confirmado" : "Obrero Listo"}
              </div>
            )}
          </div>
        </div>
      </div>
    );
  };

  return (
    <div className="min-h-screen bg-gray-100 text-gray-800 font-sans pb-12 pt-4 selection:bg-yellow-300 selection:text-red-900">
      
      {/* ENCABEZADO Y UBICACIÓN */}
      <header className="bg-gradient-to-b from-yellow-400 to-yellow-500 shadow-md border-b-4 border-red-600 mb-6 mx-2 sm:mx-4 rounded-xl overflow-hidden relative">
        <div className="max-w-6xl mx-auto px-4 py-4 sm:py-5">
          <div className="flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
            <div className="text-left flex items-center gap-3">
              <div className="bg-white p-2.5 rounded-full shadow-lg"><Shield className="text-red-600" size={30} /></div>
              <div>
                <h1 className="text-2xl sm:text-3xl font-black text-red-700 uppercase tracking-widest drop-shadow-sm">Tracker de Bubloy</h1>
                <p className="text-yellow-900 font-semibold text-sm">Asistente Táctico Competitivo</p>
              </div>
            </div>
            
            <div className="flex flex-wrap gap-2 w-full md:w-auto">
              <div className="bg-white/60 p-2 rounded-xl border border-yellow-600/30 flex items-center gap-2 relative grow md:grow-0">
                <MapPin className="text-red-600 shrink-0" size={20} />
                <select value={data.trainer.ubicacion} onChange={(e) => setData({...data, trainer: {...data.trainer, ubicacion: e.target.value}})} className="font-bold text-yellow-900 bg-transparent focus:outline-none cursor-pointer appearance-none w-full pr-6 text-base md:text-sm">
                  {LOCATIONS.map(loc => <option key={loc} value={loc}>{loc}</option>)}
                </select>
                <ChevronDown className="absolute right-2 top-1/2 transform -translate-y-1/2 text-yellow-700 pointer-events-none" size={16} />
              </div>
              <button onClick={() => setShowDataModal(true)} className="bg-gray-800 text-white p-2 rounded-xl flex items-center gap-2 shadow-sm active:bg-gray-700"><Database size={16} /> Guardado</button>
            </div>
          </div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-4">
        
        {/* CENTRO DE INTELIGENCIA DE EVENTOS */}
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
          <div className="md:col-span-1 bg-blue-900 text-white rounded-xl p-5 shadow-lg border border-blue-700 flex flex-col justify-center">
            <h2 className="text-xs font-black uppercase tracking-widest text-blue-300 mb-2 flex items-center gap-2"><Navigation size={14}/> Misión Actual</h2>
            <div className="relative">
              <select value={data.trainer.eventoActual} onChange={(e) => setData({...data, trainer: {...data.trainer, eventoActual: e.target.value}})} className="w-full font-bold text-base bg-blue-800 border-2 border-blue-600 p-3 rounded-lg focus:outline-none appearance-none pr-8 cursor-pointer">
                {GAME_EVENTS.map(ev => <option key={ev.id} value={ev.id}>{ev.label}</option>)}
              </select>
              <ChevronDown className="absolute right-3 top-1/2 transform -translate-y-1/2 text-blue-400 pointer-events-none" size={18} />
            </div>
            <p className="text-xs text-blue-200 mt-3 flex gap-2"><Map size={16} className="shrink-0 mt-0.5"/> <strong>Dato Curioso:</strong> {LOCATION_TRIVIA[data.trainer.ubicacion] || "Explorando la región."}</p>
          </div>
          
          <div className="md:col-span-2 bg-white rounded-xl p-5 shadow-sm border border-gray-200">
            <h2 className="text-sm font-black uppercase tracking-wider text-gray-800 mb-3 flex items-center gap-2"><BookOpen className="text-red-500" size={18}/> Tareas Clave y Tips del Evento</h2>
            <ul className="space-y-2">
              {currentEventData.tips.map((tip, i) => (
                <li key={i} className="flex gap-2 items-start text-sm text-gray-600 bg-gray-50 p-2.5 rounded border border-gray-100">
                  <Zap size={16} className="text-yellow-500 shrink-0 mt-0.5" />
                  <span>{tip}</span>
                </li>
              ))}
            </ul>
          </div>
        </div>

        {/* PESTAÑAS (AHORA SON 5) */}
        <div className="flex gap-2 overflow-x-auto mb-6 border-b-2 border-gray-200 hide-scrollbar whitespace-nowrap">
          <button onClick={() => setActiveTab('main')} className={`pb-3 px-3 flex items-center gap-2 font-bold text-base md:text-lg transition-colors relative flex-shrink-0 ${activeTab === 'main' ? 'text-red-600' : 'text-gray-400'}`}>
            <Sword size={20} /> Equipo Principal {activeTab === 'main' && <span className="absolute bottom-[-2px] left-0 w-full h-1 bg-red-600 rounded-t-lg"></span>}
          </button>
          <button onClick={() => setActiveTab('hm')} className={`pb-3 px-3 flex items-center gap-2 font-bold text-base md:text-lg transition-colors relative flex-shrink-0 ${activeTab === 'hm' ? 'text-yellow-600' : 'text-gray-400'}`}>
            <Briefcase size={20} /> Obreros MO {activeTab === 'hm' && <span className="absolute bottom-[-2px] left-0 w-full h-1 bg-yellow-600 rounded-t-lg"></span>}
          </button>
          <button onClick={() => setActiveTab('legendaries')} className={`pb-3 px-3 flex items-center gap-2 font-bold text-base md:text-lg transition-colors relative flex-shrink-0 ${activeTab === 'legendaries' ? 'text-purple-600' : 'text-gray-400'}`}>
            <Target size={20} /> Caza de Leyendas {activeTab === 'legendaries' && <span className="absolute bottom-[-2px] left-0 w-full h-1 bg-purple-600 rounded-t-lg"></span>}
          </button>
          <button onClick={() => setActiveTab('frontier')} className={`pb-3 px-3 flex items-center gap-2 font-bold text-base md:text-lg transition-colors relative flex-shrink-0 ${activeTab === 'frontier' ? 'text-blue-600' : 'text-gray-400'}`}>
            <Trophy size={20} /> Entrenamiento & Frente {activeTab === 'frontier' && <span className="absolute bottom-[-2px] left-0 w-full h-1 bg-blue-600 rounded-t-lg"></span>}
          </button>
          <button onClick={() => setActiveTab('calc')} className={`pb-3 px-3 flex items-center gap-2 font-bold text-base md:text-lg transition-colors relative flex-shrink-0 ${activeTab === 'calc' ? 'text-indigo-600' : 'text-gray-400'}`}>
            <Calculator size={20} /> Calculadora de Equipos {activeTab === 'calc' && <span className="absolute bottom-[-2px] left-0 w-full h-1 bg-indigo-600 rounded-t-lg"></span>}
          </button>
        </div>

        {/* --- CONTENIDO DE PESTAÑAS --- */}
        {activeTab === 'main' && <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6 pt-2">{data.mainTeam.map(p => <PokemonCard key={p.id} pokemon={p} isMain={true} />)}</div>}
        {activeTab === 'hm' && <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6 pt-2">{data.hmTeam.map(p => <PokemonCard key={p.id} pokemon={p} isMain={false} />)}</div>}
        
        {activeTab === 'legendaries' && (
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-4">
            {LEGENDARIES.map(leg => {
              const isCaught = !!data.trainer.leyendasCapturadas[leg.id];
              return (
                <div key={leg.id} onClick={() => handleToggleLegendary(leg.id)} className={`flex items-start gap-4 p-5 rounded-xl cursor-pointer transition-all border-2 shadow-sm ${isCaught ? 'bg-green-50 border-green-200' : 'bg-white border-gray-200 hover:border-purple-300'}`}>
                  <div className="shrink-0 mt-1">{isCaught ? <CheckCircle2 className="text-green-500" size={32} /> : <Square className="text-gray-300" size={32} />}</div>
                  <div className="grow">
                    <div className="flex justify-between items-center mb-1">
                      <h4 className={`font-black text-xl ${isCaught ? 'text-green-800 line-through decoration-green-400/50' : 'text-gray-800'}`}>{leg.name}</h4>
                      <span className="text-[10px] font-bold text-gray-500 bg-gray-100 px-2 py-1 rounded uppercase tracking-wider">{leg.type}</span>
                    </div>
                    {!isCaught && (
                      <div className="space-y-2 mt-3">
                        <p className="text-sm text-gray-600 bg-gray-50 p-2 rounded leading-snug border border-gray-100"><strong className="text-purple-600 flex items-center gap-1 mb-1"><MapPin size={14}/> Método de Captura:</strong> {leg.tip}</p>
                        <p className="text-sm text-gray-600 bg-purple-50 p-2 rounded leading-snug border border-purple-100"><strong className="text-purple-600 flex items-center gap-1 mb-1"><Activity size={14}/> Preparación:</strong> {leg.prep}</p>
                        <p className="text-sm text-gray-600 bg-blue-50 p-2 rounded leading-snug border border-blue-100"><strong className="text-blue-600 flex items-center gap-1 mb-1"><Sword size={14}/> Uso Competitivo:</strong> {leg.comp}</p>
                      </div>
                    )}
                    {isCaught && <span className="text-xs font-bold text-green-600 bg-green-100 px-2 py-0.5 rounded mt-2 inline-block">¡Leyenda Asegurada!</span>}
                  </div>
                </div>
              );
            })}
          </div>
        )}

        {activeTab === 'frontier' && (
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
            <div className="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
              <h3 className="font-black text-lg text-blue-900 mb-4 flex items-center gap-2"><Trophy className="text-yellow-500"/> Tienda del Frente Batalla</h3>
              <p className="text-sm text-gray-600 mb-4">Intercambia los Puntos de Batalla (BP) que ganes en la Torre Batalla por objetos competitivos clave.</p>
              <div className="space-y-3">
                {BATTLE_FRONTIER_SHOP.map((item, idx) => (
                  <div key={idx} className="bg-gray-50 p-3 rounded-lg border border-gray-100 flex justify-between items-start gap-4">
                    <div>
                      <h4 className="font-bold text-sm text-gray-800">{item.item}</h4>
                      <p className="text-xs text-gray-600 mt-1">{item.desc}</p>
                    </div>
                    <span className="font-black text-blue-700 bg-blue-100 px-2 py-1 rounded text-xs shrink-0">{item.cost}</span>
                  </div>
                ))}
              </div>
            </div>

            <div className="space-y-6">
              <div className="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
                <h3 className="font-black text-lg text-blue-900 mb-4 flex items-center gap-2"><TrendingUp className="text-green-500"/> Entrenamiento de EVs (Puntos de Esfuerzo)</h3>
                <p className="text-sm text-gray-600 mb-3">Cada Pokémon puede ganar un máximo de <strong>510 EVs</strong> en total (máximo 252 por estadística). Derrotar ciertos Pokémon otorga EVs específicos.</p>
                <div className="bg-blue-50 border border-blue-200 p-3 rounded-lg text-xs text-blue-800 mb-3">
                  <strong className="block mb-1">💡 Truco del Pokérus:</strong> Batallar con un Pokémon infectado por el Pokérus duplica los EVs ganados en combate.
                </div>
                <div className="bg-purple-50 border border-purple-200 p-3 rounded-lg text-xs text-purple-800">
                  <strong className="block mb-1">🎮 Truco de Emulador (MelonDS):</strong> Puedes clonar Carameloraros, Máx. Pociones y MTs intercambiando Pokémon equipados con estos objetos a otra ventana del emulador y recargando tu partida.
                </div>
              </div>

              <div className="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
                <h3 className="font-black text-lg text-blue-900 mb-4 flex items-center gap-2"><BookOpen className="text-purple-500"/> Guía de Naturalezas Competitivas</h3>
                <div className="overflow-x-auto">
                  <table className="w-full text-sm text-left">
                    <thead className="bg-gray-100 text-gray-600 text-xs uppercase">
                      <tr>
                        <th className="p-2 rounded-tl-lg">Naturaleza</th>
                        <th className="p-2 text-green-600 font-bold">+ Sube</th>
                        <th className="p-2 text-red-500 font-bold">- Baja</th>
                        <th className="p-2 rounded-tr-lg hidden sm:table-cell">Ideal Para</th>
                      </tr>
                    </thead>
                    <tbody className="divide-y divide-gray-100">
                      {NATURES_GUIDE.map((n, idx) => (
                        <tr key={idx} className="hover:bg-gray-50">
                          <td className="p-2 font-bold text-gray-800">{n.nature}</td>
                          <td className="p-2 text-green-600">{n.up}</td>
                          <td className="p-2 text-red-500">{n.down}</td>
                          <td className="p-2 text-gray-500 text-xs hidden sm:table-cell">{n.ideal}</td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
        )}

        {/* --- NUEVA PESTAÑA: CALCULADORA DE EQUIPOS --- */}
        {activeTab === 'calc' && (
          <div className="space-y-6">
            <div className="bg-white p-5 rounded-xl shadow-sm border border-gray-200">
              <h3 className="font-black text-lg text-indigo-900 mb-2 flex items-center gap-2"><MapPin size={18}/> Tu Caja Pokémon (Selecciona hasta 6)</h3>
              <p className="text-sm text-gray-500 mb-4">Toca los Pokémon para armar un equipo experimental y analizar su sinergia. ¡Usa tus legendarios!</p>
              
              <div className="flex flex-wrap gap-3">
                {availablePool.map(p => {
                  const isSelected = calcTeam.some(ct => ct.id === p.id);
                  return (
                    <div 
                      key={p.id} 
                      onClick={() => toggleCalcMember(p)}
                      className={`relative cursor-pointer w-16 h-16 rounded-full border-4 flex items-center justify-center transition-all ${isSelected ? 'border-indigo-500 bg-indigo-50 shadow-md scale-110' : 'border-gray-200 bg-gray-50 opacity-70 hover:opacity-100'}`}
                    >
                      <img src={getSpriteUrl(p.especie)} onError={(e) => e.target.src = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/items/poke-ball.png"} className="w-12 h-12 object-contain" style={{ imageRendering: 'pixelated' }} />
                      {isSelected && <CheckCircle2 size={16} className="absolute -top-1 -right-1 text-white bg-indigo-500 rounded-full" />}
                    </div>
                  )
                })}
              </div>
            </div>

            {calcTeam.length > 0 ? (
              <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div className="bg-indigo-900 rounded-xl p-5 shadow-lg border border-indigo-700 text-white">
                  <h3 className="font-black text-lg text-indigo-200 mb-4">Equipo Evaluado ({calcTeam.length}/6)</h3>
                  <div className="space-y-3">
                    {calcTeam.map(p => (
                      <div key={p.id} className="bg-indigo-800/50 p-3 rounded-lg flex items-center gap-3 border border-indigo-500/30">
                        <img src={getSpriteUrl(p.especie)} onError={(e) => e.target.src = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/items/poke-ball.png"} className="w-10 h-10 object-contain bg-white/10 rounded-full p-1" style={{ imageRendering: 'pixelated' }} />
                        <div>
                          <p className="font-bold text-sm">{p.mote || p.especie} <span className="text-indigo-300 font-normal text-xs ml-1">{(POKEMON_TYPES[p.especie] || []).join(" / ")}</span></p>
                          <p className="text-xs text-indigo-200 truncate mt-1">Ataques: {p.movimientos.filter(m=>m!=="-").join(", ") || "Sin registrar"}</p>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>

                <div className="space-y-6">
                  <div className="bg-white p-5 rounded-xl shadow-sm border border-red-200">
                    <h3 className="font-black text-lg text-red-700 mb-3 flex items-center gap-2"><Shield size={18}/> Análisis Defensivo</h3>
                    {analysis.criticalWeaknesses.length > 0 ? (
                      <div className="space-y-2">
                        <p className="text-sm text-red-600 font-bold mb-2">¡Cuidado! Tu equipo comparte debilidades críticas:</p>
                        {analysis.criticalWeaknesses.map(([type, count]) => (
                          <div key={type} className="flex justify-between items-center bg-red-50 p-2 rounded border border-red-100">
                            <span className="font-bold text-red-900 text-sm">Débiles a {type}</span>
                            <span className="bg-red-500 text-white text-xs px-2 py-1 rounded-full font-black">{count} Pokémon</span>
                          </div>
                        ))}
                      </div>
                    ) : (
                      <div className="bg-green-50 p-3 rounded-lg border border-green-200 flex items-center gap-2 text-green-700 text-sm font-bold">
                        <CheckCircle2 size={18}/> Excelente equilibrio defensivo. No hay debilidades superpuestas (3+).
                      </div>
                    )}
                  </div>

                  <div className="bg-white p-5 rounded-xl shadow-sm border border-green-200">
                    <h3 className="font-black text-lg text-green-700 mb-3 flex items-center gap-2"><Sword size={18}/> Cobertura Ofensiva</h3>
                    <p className="text-sm text-gray-700 mb-4">Tus ataques actuales golpean <strong>Súper Efectivo</strong> a <strong className="text-green-600 text-lg">{analysis.coverageSize}/17</strong> elementos.</p>
                    {analysis.missingCoverage.length > 0 ? (
                      <div>
                        <p className="text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">Tipos que NO cubres eficazmente:</p>
                        <div className="flex flex-wrap gap-2">
                          {analysis.missingCoverage.map(t => (
                            <span key={t} className="bg-gray-100 text-gray-600 border border-gray-300 px-2 py-1 rounded text-xs font-bold">{t}</span>
                          ))}
                        </div>
                      </div>
                    ) : (
                      <div className="bg-green-50 p-3 rounded-lg border border-green-200 flex items-center gap-2 text-green-700 text-sm font-bold">
                        <Zap size={18}/> ¡Cobertura Perfecta! Tienes ataques para destruir a cualquier tipo elemental.
                      </div>
                    )}
                  </div>
                </div>
              </div>
            ) : (
              <div className="text-center py-12 bg-white rounded-xl border-2 border-dashed border-gray-300 text-gray-400">
                <Calculator size={48} className="mx-auto mb-4 opacity-50" />
                <p className="font-bold text-lg">El Laboratorio está vacío</p>
                <p className="text-sm">Selecciona Pokémon de tu caja arriba para comenzar el análisis.</p>
              </div>
            )}
          </div>
        )}
      </main>

      {/* MODAL DE EDICIÓN AVANZADO */}
      {editingPokemon && (
        <div className="fixed inset-0 bg-black/80 flex items-center justify-center p-4 z-50 backdrop-blur-sm">
          <div className="bg-white rounded-2xl shadow-2xl w-full max-w-xl overflow-hidden flex flex-col max-h-[95vh]">
            <div className="bg-gray-900 text-white p-4 flex justify-between items-center shrink-0 border-b-4 border-red-500">
              <h3 className="font-black text-lg uppercase flex items-center gap-2"><Activity className="text-red-400" /> Analizando a {editingPokemon.mote}</h3>
              <button onClick={() => setEditingPokemon(null)} className="text-gray-400 hover:text-white bg-gray-800 p-2 rounded-full"><X size={20} /></button>
            </div>
            
            <div className="p-4 sm:p-6 space-y-6 overflow-y-auto grow bg-gray-50">
              <div className="flex gap-4 items-center bg-white p-4 rounded-xl shadow-sm border border-gray-200">
                <div className="relative shrink-0">
                  <img src={getSpriteUrl(editingPokemon.especie)} onError={(e) => e.target.src = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/items/poke-ball.png"} className="w-24 h-24 bg-gradient-to-br from-gray-100 to-gray-200 rounded-full border-4 border-gray-300 p-2 object-contain shadow-inner" style={{imageRendering: 'pixelated'}} />
                  {EVOLUTIONS[editingPokemon.especie] && (
                    <button onClick={handleEvolve} className="absolute -bottom-2 -right-2 bg-purple-600 text-white px-3 py-1 text-xs font-bold uppercase rounded-full shadow-lg active:scale-95 flex gap-1 items-center">
                      <ArrowUpCircle size={14}/> Evolucionar
                    </button>
                  )}
                </div>
                <div className="grow space-y-1">
                  <label className="text-[10px] font-black text-gray-400 uppercase tracking-widest">Nivel Actual</label>
                  <input type="number" value={editingPokemon.nivel} onChange={(e) => setEditingPokemon({...editingPokemon, nivel: parseInt(e.target.value) || ''})} className="w-full text-3xl font-black p-2 border-b-2 border-gray-300 focus:border-red-500 focus:outline-none bg-transparent" />
                </div>
              </div>

              {/* SECCIÓN DE EVOLUCIÓN DENTRO DEL MODAL */}
              {EVOLUTIONS[editingPokemon.especie] && (
                <div className="bg-purple-50 p-4 rounded-xl shadow-sm border border-purple-200">
                  <h4 className="text-[10px] uppercase font-black tracking-widest text-purple-600 mb-2 flex items-center gap-1"><ArrowUpCircle size={12}/> Guía de Evolución</h4>
                  <p className="text-sm text-purple-900 mb-3"><strong>{editingPokemon.especie}</strong> evoluciona a <strong>{EVOLUTIONS[editingPokemon.especie]}</strong>.</p>
                  <p className="text-xs text-purple-800 mb-4 bg-purple-100 p-2 rounded border border-purple-200"><strong>Método:</strong> {EVOLUTION_METHODS[EVOLUTIONS[editingPokemon.especie]] || "Sube de nivel."}</p>
                </div>
              )}

              {/* Sección Medio: Objeto */}
              <div className="bg-white p-4 rounded-xl shadow-sm border border-gray-200">
                <label className="text-[10px] font-black text-gray-400 uppercase tracking-widest flex items-center gap-1 mb-2"><Briefcase size={12}/> Objeto Estratégico</label>
                <div className="relative">
                  <select value={COMMON_ITEMS.includes(editingPokemon.objeto) ? editingPokemon.objeto : "Otro"} onChange={(e) => setEditingPokemon({...editingPokemon, objeto: e.target.value === "Otro" ? "" : e.target.value})} className="w-full p-3 pr-10 bg-gray-50 border border-gray-300 rounded-lg font-bold text-base appearance-none focus:ring-2 focus:ring-blue-400 focus:outline-none cursor-pointer">
                    {COMMON_ITEMS.map(i => <option key={i} value={i}>{i === "Ninguno" ? "Ninguno" : i}</option>)}
                  </select>
                  <ChevronDown className="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 pointer-events-none" />
                </div>
                
                {/* AVISO DE OBJETO ACTIVO */}
                {editingPokemon.objeto && ITEMS_INFO[editingPokemon.objeto] && (
                  <div className={`mt-3 p-3 rounded-lg border text-xs leading-relaxed ${ITEMS_INFO[editingPokemon.objeto].warn.includes('⚠️') || ITEMS_INFO[editingPokemon.objeto].warn.includes('🚨') ? 'bg-red-50 border-red-200 text-red-900' : 'bg-blue-50 border-blue-200 text-blue-900'}`}>
                    <p className="mb-1 flex gap-1 items-start"><MapPin size={14} className="shrink-0 mt-0.5"/> <span><strong>Ubicación:</strong> {ITEMS_INFO[editingPokemon.objeto].loc}</span></p>
                    <p className="mb-1 flex gap-1 items-start"><Info size={14} className="shrink-0 mt-0.5"/> <span><strong>Efecto:</strong> {ITEMS_INFO[editingPokemon.objeto].warn}</span></p>
                    <p className="flex gap-1 items-start"><Target size={14} className="shrink-0 mt-0.5"/> <span><strong>Ideal para:</strong> {ITEMS_INFO[editingPokemon.objeto].rec}</span></p>
                  </div>
                )}
              </div>

              {/* Sección Inferior: Movimientos */}
              <div className="bg-white p-4 rounded-xl shadow-sm border border-gray-200">
                <div className="flex justify-between items-end mb-3">
                  <label className="text-[10px] font-black text-gray-400 uppercase tracking-widest flex items-center gap-1"><Crosshair size={12}/> Moveset Actual</label>
                  {editingPokemon.isMain && PERFECT_SETS[editingPokemon.especie] && (
                    <span className="text-[9px] font-bold text-purple-600 bg-purple-100 px-2 py-0.5 rounded-full uppercase tracking-wider">Ver Set Perfecto Abajo 👇</span>
                  )}
                </div>

                <div className="grid grid-cols-1 sm:grid-cols-2 gap-3 mb-4">
                  {editingPokemon.movimientos.map((mov, idx) => {
                    const sugerencias = editingPokemon.isMain ? (PERFECT_SETS[editingPokemon.especie] || RECOMMENDED_MOVES[editingPokemon.especie] || []) : (RECOMMENDED_MOVES[editingPokemon.especie] || ["Corte", "Fuerza"]);
                    const opciones = Array.from(new Set(["-", mov, ...sugerencias, ...GENERIC_MOVES])).filter(Boolean);
                    const isWeak = editingPokemon.isMain && WEAK_MOVES[mov] && editingPokemon.nivel >= 30;
                    const isDupe = editingPokemon.isMain && globalTMCounts[mov] > 1 && MOVES_DB[mov]?.unique;
                    
                    return (
                      <div className="relative" key={idx}>
                        <select value={mov || "-"} onChange={(e) => updateMove(idx, e.target.value === "-" ? "" : e.target.value)} className={`w-full p-3 pr-8 rounded-lg text-base sm:text-sm font-bold appearance-none border-2 focus:outline-none focus:ring-2 focus:ring-red-400 cursor-pointer ${isWeak || isDupe ? 'border-red-400 bg-red-50 text-red-900' : 'border-gray-200 bg-gray-50 text-gray-800'}`}>
                          {opciones.map(opt => <option key={opt} value={opt}>{opt === "-" ? "--- Vacío ---" : opt}</option>)}
                        </select>
                        <ChevronDown className="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 pointer-events-none" size={14} />
                        
                        {mov && mov !== "-" && MOVES_DB[mov] && (
                          <p className="text-[9px] font-bold text-gray-500 mt-1 uppercase truncate px-1 flex gap-1 items-center"><MapPin size={10}/> {MOVES_DB[mov].loc}</p>
                        )}
                      </div>
                    );
                  })}
                </div>

                {/* Visor de Set Perfecto */}
                {editingPokemon.isMain && PERFECT_SETS[editingPokemon.especie] && (
                  <div className="bg-gradient-to-r from-purple-900 to-indigo-900 p-3 rounded-lg text-white mt-4 shadow-inner">
                    <h4 className="text-[10px] uppercase font-black tracking-widest text-purple-300 mb-2 flex items-center gap-1"><Target size={12}/> Meta: El Set Perfecto</h4>
                    <div className="flex flex-wrap gap-1.5">
                      {PERFECT_SETS[editingPokemon.especie].map(m => (
                        <span key={m} className={`px-2 py-1 rounded text-[10px] font-bold border ${editingPokemon.movimientos.includes(m) ? 'bg-green-500/20 border-green-400 text-green-300' : 'bg-black/40 border-purple-500/30 text-gray-300'}`}>
                          {m} {editingPokemon.movimientos.includes(m) && '✓'}
                        </span>
                      ))}
                    </div>
                  </div>
                )}
              </div>
            </div>
            
            <div className="p-4 bg-white border-t border-gray-200 shrink-0">
              <button onClick={handleSave} className="w-full py-4 bg-gradient-to-r from-red-600 to-red-500 text-white font-black uppercase tracking-widest rounded-xl shadow-lg active:scale-95 transition-transform flex justify-center items-center gap-2">
                <Save size={20} /> Guardar Cambios
              </button>
            </div>
          </div>
        </div>
      )}

      {/* MODAL DE GESTIÓN DE DATOS */}
      {showDataModal && (
        <div className="fixed inset-0 bg-black/80 flex items-center justify-center p-4 z-[60] backdrop-blur-sm">
          <div className="bg-white rounded-2xl p-6 max-w-md w-full shadow-2xl flex flex-col max-h-[90vh]">
            <div className="flex justify-between items-center mb-4">
              <h3 className="text-xl font-black text-gray-800 flex items-center gap-2"><Database className="text-blue-500" /> Gestión de Datos</h3>
              <button onClick={() => setShowDataModal(false)} className="text-gray-400 hover:text-gray-800"><X size={24} /></button>
            </div>
            
            <div className="overflow-y-auto grow space-y-6">
              <p className="text-sm text-gray-600">Exporta tu progreso para no perderlo, importa una partida guardada o reinicia todo de fábrica.</p>
              
              <div className="bg-blue-50 p-4 rounded-xl border border-blue-200">
                <h4 className="font-bold text-blue-900 mb-2 flex items-center gap-2"><Download size={16}/> Exportar (Guardar)</h4>
                <p className="text-xs text-blue-800 mb-3">Copia este código y guárdalo en tus notas para restaurar tu Tracker en cualquier dispositivo.</p>
                <button onClick={copyToClipboard} className="w-full py-2 bg-blue-600 text-white font-bold rounded-lg shadow active:bg-blue-700 flex items-center justify-center gap-2">
                  <Copy size={16}/> Copiar Datos de Guardado
                </button>
              </div>

              <div className="bg-green-50 p-4 rounded-xl border border-green-200">
                <h4 className="font-bold text-green-900 mb-2 flex items-center gap-2"><Upload size={16}/> Importar (Cargar)</h4>
                <p className="text-xs text-green-800 mb-3">Pega el código de guardado aquí para recuperar tu progreso anterior.</p>
                <textarea 
                  value={importText} 
                  onChange={(e) => setImportText(e.target.value)}
                  placeholder='Pega tu código JSON aquí...'
                  className="w-full p-2 border border-green-300 rounded text-xs h-24 mb-3 focus:outline-none focus:ring-2 focus:ring-green-500"
                />
                {importError && <p className="text-xs text-red-600 font-bold mb-2">{importError}</p>}
                <button onClick={handleImport} className="w-full py-2 bg-green-600 text-white font-bold rounded-lg shadow active:bg-green-700 flex items-center justify-center gap-2">
                  <Upload size={16}/> Restaurar Partida
                </button>
              </div>

              <div className="bg-red-50 p-4 rounded-xl border border-red-200">
                <h4 className="font-bold text-red-900 mb-2 flex items-center gap-2"><RotateCcw size={16}/> Resetear Todo</h4>
                <p className="text-xs text-red-800 mb-3">Esta acción borrará tus modificaciones y volverá al equipo por defecto. <strong>No se puede deshacer</strong>.</p>
                <button onClick={() => { if(window.confirm("¿Seguro que quieres borrar todo?")) { setData(defaultData); setShowDataModal(false); } }} className="w-full py-2 bg-red-600 text-white font-bold rounded-lg shadow active:bg-red-700 flex items-center justify-center gap-2">
                  <AlertCircle size={16}/> Reiniciar de Fábrica
                </button>
              </div>
            </div>
          </div>
        </div>
      )}

      <style dangerouslySetInnerHTML={{__html: `.hide-scrollbar::-webkit-scrollbar { display: none; } .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }`}} />
    </div>
  );
}