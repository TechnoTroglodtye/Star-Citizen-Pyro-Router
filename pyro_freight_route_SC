import math
import time
import itertools
import ctypes
import os

def calculate_distance(point1, point2):
    """Calculates 3D Euclidean distance between two spatial coordinates."""
    return math.sqrt(sum((a - b) ** 2 for a, b in zip(point1, point2)))

def display_single_menu(title, options_list):
    """Displays a numbered menu for single selection with column-major ordering."""
    print(f"\n--- {title} ---")
    cols = 2
    rows = math.ceil(len(options_list) / cols)
    
    for r in range(rows):
        row_str = ""
        for c in range(cols):
            idx = c * rows + r
            if idx < len(options_list):
                row_str += f"[{idx + 1:2d}] {options_list[idx]:<35}"
        print(row_str)
        
    while True:
        try:
            choice = int(input("\nSelect a number: "))
            if 1 <= choice <= len(options_list):
                return options_list[choice - 1]
            print("Invalid selection. Try again.")
        except ValueError:
            print("Numbers only.")

def display_multi_menu(title, options_list):
    """Displays a numbered menu for multiple selections with column-major ordering."""
    print(f"\n--- {title} ---")
    cols = 2
    rows = math.ceil(len(options_list) / cols)
    
    for r in range(rows):
        row_str = ""
        for c in range(cols):
            idx = c * rows + r
            if idx < len(options_list):
                row_str += f"[{idx + 1:2d}] {options_list[idx]:<35}"
        print(row_str)
        
    print("\n[ 0] DONE (Proceed to next step)")
    
    selected = []
    while True:
        if selected:
            print(f"\nCurrent Selection: {', '.join(selected)}")
            
        choice = input("Select number(s) comma-separated (or 0 when done): ").strip()
        
        if choice == '0':
            return selected
            
        try:
            indices = [int(x.strip()) for x in choice.split(',') if x.strip()]
            for idx in indices:
                if 1 <= idx <= len(options_list):
                    item = options_list[idx - 1]
                    if item not in selected:
                        selected.append(item)
                else:
                    print(f"Number {idx} is out of range.")
        except ValueError:
            print("Invalid format. Use numbers.")

def get_freight_and_locations(phase_name, location_names, commodity_list):
    """Handles the Freight-First -> Individual Dropoff workflow."""
    manifest_dict = {}
    print(f"\n================ {phase_name.upper()} ================")
    while True:
        comods = display_multi_menu(f"SELECT FREIGHT FOR {phase_name.upper()}", commodity_list)
        if not comods:
            break
            
        # Loop through each selected commodity and ask for its specific dropoffs
        for single_comod in comods:
            locs = display_multi_menu(f"SELECT DROPOFF LOCATIONS FOR: {single_comod}", location_names)
            for loc in locs:
                if loc not in manifest_dict:
                    manifest_dict[loc] = set()
                manifest_dict[loc].add(single_comod)
            
    return [{"loc": k, "comods": list(v)} for k, v in manifest_dict.items()]

def calculate_optimal_route(start_loc, p_list, d_list, db):
    """
    Uses an exact brute-force TSP solver for combined Pickups and Dropoffs.
    Guarantees the absolute shortest path and naturally resolves duplicates.
    """
    best_dist = float('inf')
    best_p_route = []
    best_d_route = []
    
    for p_perm in itertools.permutations(p_list):
        for d_perm in itertools.permutations(d_list):
            current_dist = 0.0
            curr_loc = start_loc
            
            for stop in p_perm:
                current_dist += calculate_distance(db[curr_loc], db[stop["loc"]])
                curr_loc = stop["loc"]
                
            for stop in d_perm:
                current_dist += calculate_distance(db[curr_loc], db[stop["loc"]])
                curr_loc = stop["loc"]
                
            if current_dist < best_dist:
                best_dist = current_dist
                best_p_route = list(p_perm)
                best_d_route = list(d_perm)
                
    return best_p_route, best_d_route, best_dist

# ==============================================================================
# THE GRAND TOUR LOGISTICS DATABASE (100% COVERAGE)
# ==============================================================================

pyro_locations_dict = {
    "Ashland [Ignis]": (-3965622221.893461, -42774737330.020805, 265976.590180),
    "Bueno Ravine [Bloom]": (-16104019781.392450, 7587544650.780657, -55043.012121),
    "Canard View [Terminus]": (-48118473390.856293, 48503311205.014427, -104210.228612),
    "Chawla's Beach [Pyro IV]": (-3704202834.725277, -43071407531.141129, 189342.610523),
    "Checkmate [PYR2-L4]": (10550863484.671803, -959299359.503702, 2571755.756648),
    "Dudley & Daughters [PYR6-L4]": (-62062402883.674278, -28602577028.153858, -10764520.962047),
    "Endgame [PYR6-L3]": (52140944773.969345, -44113647219.894402, 2734498.950527),
    "Fallow Field [Pyro IV]": (-3704313644.110620, -43071728311.582306, 262110.714615),
    "Gaslight [PYR5-L2]": (-4296211488.205238, -47014546578.037971, 17666075.218870),
    "Jackson's Swap [Monox]": (6056859904.668401, -8725134703.798891, -273224.848647),
    "Last Landings [Terminus]": (-48118047765.140869, 48503305155.670471, -7951.434754),
    "Megumi [PYR6-L5]": (29108873984.264660, 61780158835.532448, 12706257.479343),
    "Nyx Gateway [Pyro]": (-35275902938.533424, -36052293448.741425, 5231.811273),
    "Orbituary [Bloom Orbit]": (-16104236039.342888, 7587206482.425430, 224851.093576),
    "Patch City [PYR3-L3]": (14526918709.542616, -10272844456.636803, 2753405.018350),
    "Rat's Nest [PYR5-L5]": (-42308388155.284317, -7276095571.882400, 18512845.947221),
    "Rod's Fuel [PYR5-L4]": (41418349978.461685, -11259078248.605715, -9517934.047968),
    "Ruin Station [Pyro VI]": (-48117838232.509102, 48503143589.652634, -100441.229385),
    "Rustville [Pyro I]": (-7920868603.014924, -2387347665.596297, 171785.017761),
    "Sacren's Plot [Pyro IV]": (-3703973430.714950, -43071855333.722099, 195716.179846),
    "Scarper's Turn [Terminus]": (-48118037051.478600, 48503290214.470848, 233.234754),
    "Seer's Canyon [Vatra]": (-3889610903.511387, -42688332205.253975, -119699.379535),
    "Shepherd's Rest [Bloom]": (-16104122890.370398, 7588219046.939068, -202689.271655),
    "Stanton Gateway [Pyro]": (34178594701.877945, 40592016062.759071, 5998.743905),
    "Starlight Service [PYR3-L1]": (-14605356844.761406, 6564827390.369520, 1357943.138658),
    "Sunset Mesa [Monox]": (6057708176.970610, -8724811417.020597, -272094.597297),
    "The Golden Riviera [Bloom]": (-16104266443.968779, 7587755523.508147, 5201.752462)
}

location_names = sorted(list(pyro_locations_dict.keys()))

commodity_list = sorted([
    "Agricium", "Agricultural Supplies", "Altruciatoxin", "Aluminum", "Amioshi Plague", 
    "Ammonia", "Argon", "Astatine", "Atlasium", "Audio-Visual Equipment", 
    "Beryl", "Bexalite", "Bioplastic", "Borase (Ore)", "Carbon", 
    "Carbon-Silk", "Chlorine", "CK13-GID Seed Blend", "Cobalt", "Compboard", 
    "Construction Materials", "Copper", "Corundum", "Degnous Root", "Diamond", 
    "Diamond Laminate", "Distilled Spirits", "Dymantium", "DynaFlex", "E'tam", 
    "Elespo", "Fluorine", "Fresh Food", "Gasping Weevil Eggs", "Gold", 
    "Golden Medmon", "Helium", "Hephaestanite", "Human Food Bars", "Hydrogen", 
    "Iodine", "Iron", "Laranite", "LifeCure Medsticks", "Medical Supplies", 
    "Mercury", "Methane", "Neon", "Nitrogen", "Omnapoxy", 
    "Organics", "Osoian Hides", "Partillium", "Pitambu", "Potassium", 
    "Pressurized Ice", "Processed Food", "Quartz", "Ranta Dung", "Recycled Material Composite", 
    "Revenant Tree Pollen", "Scrap", "Silicon", "Steel", "Stileron", 
    "Stims", "Sunset Berries", "Taranite", "Thermalfoam", "Tin", 
    "Titanium", "Tritium", "Tungsten", "Waste", "WiDoW", "Xa'Pyen"
])

if __name__ == "__main__":
    # Force the console window to maximize on Windows startup
    if os.name == 'nt':
        try:
            hwnd = ctypes.windll.kernel32.GetConsoleWindow()
            if hwnd:
                ctypes.windll.user32.ShowWindow(hwnd, 3) # 3 is SW_MAXIMIZE
        except Exception:
            pass

    print("========================================")
    print("  PRO PYRO TRADE ROUTE CONFIGURATOR     ")
    print("========================================")
    
    current_location = display_single_menu("SELECT CURRENT LOCATION", location_names)
    
    print("\n================ ACQUISITION PHASE ================")
    pickup_locs = display_multi_menu("SELECT ALL PICKUP LOCATIONS", location_names)
    if not pickup_locs:
        print("Error: Minimum one pickup required.")
        exit()
    # Create the manifest with empty commodity lists for the solver
    pickup_manifest = [{"loc": loc, "comods": []} for loc in pickup_locs]
        
    dropoff_manifest = get_freight_and_locations("Delivery Phase", location_names, commodity_list)
    if not dropoff_manifest:
        print("Error: Minimum one dropoff required.")
        exit()

    print("\n[SYSTEM] Running exact TSP brute-force permutations...")
    time.sleep(1)
    
    pickup_route, dropoff_route, total_dist = calculate_optimal_route(
        current_location, 
        pickup_manifest, 
        dropoff_manifest, 
        pyro_locations_dict
    )
    
    print("\n================================================================================")
    print("                              OPTIMIZED FLIGHT PLAN                             ")
    print("================================================================================")
    print(f"STARTING POINT: {current_location}\n")
    
    print(f"{'#':<3} | {'LOCATION':<30} | {'COMMODITY'}")
    print("-" * 80)
    
    print("PHASE 1: ACQUISITION")
    for i, stop in enumerate(pickup_route, 1):
        print(f"{i:<3} | {stop['loc']:<30} | [+] Cargo Pickup")
        
    print("-" * 80)
    print("PHASE 2: DELIVERY")
    for i, stop in enumerate(dropoff_route, len(pickup_route) + 1):
        comods_string = ", ".join(stop['comods'])
        print(f"{i:<3} | {stop['loc']:<30} | [-] {comods_string}")
        
    print("================================================================================")
    print(f"-> Total Estimated Quantum Distance: {total_dist:,.2f} units")
    print("================================================================================")
    
    input("\nPress ENTER to close the nav computer...")
