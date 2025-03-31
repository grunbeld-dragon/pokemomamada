import random
import difflib
from enum import Enum
from typing import Dict, Tuple, List, Optional

# ================== ENUMS ==================
class PokemonType(Enum):
    FIRE = "fuego"
    WATER = "agua"
    PLANT = "planta"
    ELECTRIC = "electrico"
    NORMAL = "normal"
    FLYING = "volador"
    ROCK = "roca"
    PSYCHIC = "psiquico"

# ================== CLASSES ==================
class Pokemon:
    def __init__(
        self,
        name: str,
        level: int,
        type_: PokemonType,
        hp: int,
        attacks: Dict[str, Tuple[int, PokemonType]],
        evolutions: Optional[List[Tuple[int, str]]] = None
    ) -> None:
        """
        Inicializa un Pokémon con sus atributos básicos.
        evolutions: lista de tuplas (nivel_requerido, nuevo_nombre) que define su cadena evolutiva.
                    Se espera que estén ordenadas en forma ascendente.
        """
        self.name = name
        self.level = level
        self.type = type_
        self.hp = hp
        self.max_hp = hp
        self.attacks = attacks
        self.evolutions = evolutions if evolutions is not None else []

    def attack(self, other: "Pokemon", attack_name: str) -> None:
        """
        Realiza un ataque a otro Pokémon, tolerando errores en la escritura del nombre.
        """
        if attack_name not in self.attacks:
            posibles = difflib.get_close_matches(attack_name, list(self.attacks.keys()), n=1, cutoff=0.6)
            if posibles:
                correccion = posibles[0]
                print(f"Se interpretó '{attack_name}' como '{correccion}'.")
                attack_name = correccion
            else:
                print(f"{self.name} no tiene el ataque '{attack_name}'.")
                return

        power, atk_type = self.attacks[attack_name]
        damage = calculate_damage(power, self.type.value, atk_type.value, other.type.value)
        other.hp -= damage
        other.hp = max(0, other.hp)
        print(f"{self.name} usó '{attack_name}'! Causó {damage} de daño a {other.name}.")

        if other.hp == 0:
            print(f"{other.name} ha sido derrotado!")

    def level_up(self) -> None:
        """
        Incrementa el nivel del Pokémon en 1, aumenta sus estadísticas y verifica si evoluciona.
        """
        self.level += 1
        self.max_hp += 10
        self.hp = self.max_hp
        print(f"{self.name} ha subido al nivel {self.level}!")

        # Verificar si hay una evolución pendiente para el nivel actual
        if self.evolutions and self.evolutions[0][0] <= self.level:
            new_form = self.evolutions.pop(0)[1]
            print(f"¡{self.name} ha evolucionado a {new_form}!")
            self.name = new_form

    def apply_level_increase(self, levels: int) -> None:
        """
        Incrementa el nivel del Pokémon en 'levels' unidades.
        Se llama al método level_up repetidamente para aplicar las mejoras y verificar evoluciones.
        """
        for _ in range(levels):
            self.level_up()

class Battle:
    def __init__(self, player: Pokemon, opponent: Pokemon) -> None:
        """
        Inicializa una batalla entre el Pokémon del jugador y un oponente.
        """
        self.player = player
        self.opponent = opponent

    def take_turn(self) -> None:
        """
        Ejecuta un turno de la batalla, mostrando el repertorio de ataques antes de pedir la acción.
        """
        print(f"\nTu Pokémon: {self.player.name} - HP: {self.player.hp}/{self.player.max_hp} - Nivel: {self.player.level}")
        print(f"Oponente: {self.opponent.name} - HP: {self.opponent.hp}/{self.opponent.max_hp} - Nivel: {self.opponent.level}")
        
        # Mostrar el repertorio de ataques del jugador en cada turno
        show_attacks(self.player)
        
        attack_name = input("Elige tu ataque (o escribe 'pocion' para curarte): ").strip()
        if attack_name.lower() == "pocion":
            self.use_potion(self.player)
        else:
            self.player.attack(self.opponent, attack_name)

        if self.opponent.hp > 0:
            enemy_attack = random.choice(list(self.opponent.attacks.keys()))
            print(f"\nEl oponente ha elegido su ataque: {enemy_attack}")
            self.opponent.attack(self.player, enemy_attack)

    def use_potion(self, pokemon: Pokemon) -> None:
        """
        Usa una poción para recuperar HP al Pokémon.
        """
        heal = min(20, pokemon.max_hp - pokemon.hp)
        pokemon.hp += heal
        print(f"{pokemon.name} ha usado una poción y recuperó {heal} de HP.")

# ================== FUNCTIONS ==================
def calculate_damage(power: int, atk_type: str, move_type: str, defense_type: str) -> int:
    """
    Calcula el daño de un ataque basándose en la efectividad entre tipos.
    """
    effectiveness = {
        ("fuego", "planta"): 2.0,
        ("agua", "fuego"): 2.0,
        ("planta", "agua"): 2.0,
        ("fuego", "agua"): 0.5,
        ("agua", "planta"): 0.5,
        ("planta", "fuego"): 0.5
    }
    modifier = effectiveness.get((move_type, defense_type), 1.0)
    return int(power * modifier)

def select_pokemon(pokemons: List[Pokemon]) -> Pokemon:
    """
    Permite al usuario seleccionar un Pokémon de una lista, validando la entrada.
    """
    print("Selecciona tu Pokémon:")
    for i, pkm in enumerate(pokemons):
        print(f"{i + 1}. {pkm.name} - Tipo: {pkm.type.value}")
    
    while True:
        try:
            choice = int(input("Elige un Pokémon (ingresa el número): ")) - 1
            if 0 <= choice < len(pokemons):
                return pokemons[choice]
            else:
                print("Selección inválida. Por favor, ingresa un número válido.")
        except ValueError:
            print("Entrada inválida. Por favor, ingresa un número.")

def show_attacks(pokemon: Pokemon) -> None:
    """
    Muestra el repertorio de ataques del Pokémon.
    """
    print(f"\nRepertorio de ataques de {pokemon.name}:")
    for attack, (power, atk_type) in pokemon.attacks.items():
        print(f"- {attack}: Poder {power}, Tipo {atk_type.value}")
    print("Recuerda escribir el nombre del ataque tal como aparece (se tolerarán errores leves).")

def create_pokemons() -> List[Pokemon]:
    """
    Crea y retorna una lista de 10 Pokémon con sus ataques y cadenas evolutivas.
    Las evoluciones se producen a nivel 5 y nivel 10.
    """
    charmander = Pokemon("Charmander", 1, PokemonType.FIRE, 100, {
        "Ascuas": (20, PokemonType.FIRE),
        "Garra": (15, PokemonType.NORMAL),
        "Llama": (25, PokemonType.FIRE),
        "Embestida": (18, PokemonType.NORMAL),
        "Sofoco": (30, PokemonType.FIRE)
    }, evolutions=[(5, "Charmeleon"), (10, "Charizard")])

    bulbasaur = Pokemon("Bulbasaur", 1, PokemonType.PLANT, 100, {
        "Látigo Cepa": (20, PokemonType.PLANT),
        "Placaje": (15, PokemonType.NORMAL),
        "Hoja Afilada": (25, PokemonType.PLANT),
        "Drenadoras": (18, PokemonType.PLANT),
        "Latigazo": (22, PokemonType.NORMAL)
    }, evolutions=[(5, "Ivysaur"), (10, "Venusaur")])

    squirtle = Pokemon("Squirtle", 1, PokemonType.WATER, 100, {
        "Pistola Agua": (20, PokemonType.WATER),
        "Concha": (10, PokemonType.NORMAL),
        "Chorro de Agua": (25, PokemonType.WATER),
        "Burbuja": (18, PokemonType.WATER),
        "Mordisco": (15, PokemonType.NORMAL)
    }, evolutions=[(5, "Wartortle"), (10, "Blastoise")])

    pikachu = Pokemon("Pikachu", 1, PokemonType.ELECTRIC, 100, {
        "Impactrueno": (20, PokemonType.ELECTRIC),
        "Rayo": (25, PokemonType.ELECTRIC),
        "Voltio": (15, PokemonType.ELECTRIC),
        "Chispa": (18, PokemonType.ELECTRIC),
        "Electrobola": (28, PokemonType.ELECTRIC)
    }, evolutions=[(5, "Raichu")])

    eevee = Pokemon("Eevee", 1, PokemonType.NORMAL, 100, {
        "Mordisco": (15, PokemonType.NORMAL),
        "Placaje": (15, PokemonType.NORMAL),
        "Rápido": (20, PokemonType.NORMAL),
        "Gruñido": (10, PokemonType.NORMAL),
        "Cola de Hierro": (22, PokemonType.NORMAL)
    }, evolutions=[(5, "Vaporeon")])

    pidgey = Pokemon("Pidgey", 1, PokemonType.FLYING, 100, {
        "Picotazo": (15, PokemonType.NORMAL),
        "Graznido": (10, PokemonType.NORMAL),
        "Ataque Aéreo": (20, PokemonType.FLYING),
        "Alarido": (18, PokemonType.NORMAL),
        "Corte": (22, PokemonType.NORMAL)
    }, evolutions=[(5, "Pidgeotto"), (10, "Pidgeot")])

    rattata = Pokemon("Rattata", 1, PokemonType.NORMAL, 100, {
        "Mordisco": (15, PokemonType.NORMAL),
        "Placaje": (15, PokemonType.NORMAL),
        "Colmillo": (18, PokemonType.NORMAL),
        "Ataque Rápido": (20, PokemonType.NORMAL),
        "Garra": (12, PokemonType.NORMAL)
    }, evolutions=[(5, "Raticate")])

    jigglypuff = Pokemon("Jigglypuff", 1, PokemonType.NORMAL, 100, {
        "Canto": (10, PokemonType.NORMAL),
        "Placaje": (15, PokemonType.NORMAL),
        "Beso Dulce": (20, PokemonType.NORMAL),
        "Golpecito": (18, PokemonType.NORMAL),
        "Susurro": (12, PokemonType.NORMAL)
    }, evolutions=[(5, "Wigglytuff")])

    geodude = Pokemon("Geodude", 1, PokemonType.ROCK, 100, {
        "Roca Afilada": (20, PokemonType.ROCK),
        "Placaje": (15, PokemonType.NORMAL),
        "Deslizamiento": (18, PokemonType.ROCK),
        "Golpe Rudo": (22, PokemonType.NORMAL),
        "Avalancha": (25, PokemonType.ROCK)
    }, evolutions=[(5, "Graveler"), (10, "Golem")])

    zubat = Pokemon("Zubat", 1, PokemonType.FLYING, 100, {
        "Picotazo": (15, PokemonType.NORMAL),
        "Chirrido": (12, PokemonType.NORMAL),
        "Ataque Aéreo": (20, PokemonType.FLYING),
        "Mordisco": (18, PokemonType.NORMAL),
        "Eco": (22, PokemonType.NORMAL)
    }, evolutions=[(5, "Golbat")])

    # Agregamos 10 Pokémon en total
    return [charmander, bulbasaur, squirtle, pikachu, eevee, pidgey, rattata, jigglypuff, geodude, zubat]

def main() -> None:
    """
    Función principal que inicializa y ejecuta el juego.
    Permite volver a jugar al finalizar una batalla.
    """
    while True:
        pokemons = create_pokemons()

        # Selección de Pokémon
        player_pokemon = select_pokemon(pokemons)
        show_attacks(player_pokemon)
        
        # Selección del oponente, evitando que sea el mismo que el del jugador
        opponent_candidates = [p for p in pokemons if p != player_pokemon]
        opponent_pokemon = random.choice(opponent_candidates)
        print(f"\nTe enfrentarás a {opponent_pokemon.name}.\n")

        battle = Battle(player_pokemon, opponent_pokemon)

        # Bucle de batalla
        while player_pokemon.hp > 0 and opponent_pokemon.hp > 0:
            battle.take_turn()

        # Determinar el resultado de la batalla
        if opponent_pokemon.hp == 0:
            resultado = "ganado"
            level_increase = 3
        else:
            resultado = "perdido"
            level_increase = 1

        print(f"\n¡Fin de la batalla! Has {resultado} la batalla.")
        print(f"Tu Pokémon ganó {level_increase} niveles en esta batalla.")

        # Aplicar incremento de niveles
        player_pokemon.apply_level_increase(level_increase)
        print(f"Ahora, {player_pokemon.name} está en el nivel {player_pokemon.level}.\n")

        # Preguntar si desea volver a jugar
        jugar_de_nuevo = input("¿Deseas volver a enfrentarte? (s/n): ").strip().lower()
        if jugar_de_nuevo != "s":
            print("¡Gracias por jugar!")
            break

if __name__ == "__main__":
    main()
