[Tool] Pyro Route Optimizer - Custom 3D Nav Planner for Mission Stacking & Cargo Hauling

Got frustrated stacking freight and struggling to plan out efficient quantum jumps in the Pyro system. To fix this, I manually mapped all the 3D XYZ coordinates from the Pyro terminal locations listed in Schaulers and vibe-coded a custom routing tool for Star Citizen space logisticians.

What it does:

Asks for your current starting location.

Lets you select multiple cargo pickup locations in Phase 1.

Maps specific commodities to their exact dropoff destinations in Phase 2 (supports multi-dropoffs).

Uses an exact brute-force TSP (Traveling Salesperson Problem) solver to calculate the absolute shortest 3D distance and spits out the fastest possible quantum flight plan.

It was designed specifically with stacking delivery missions in mind to minimize backtracking and quantum travel time, but it works perfectly as a standard buy/sell trade route optimizer too.

How to run it:
The tool is a raw Python (.py) file. You can run it directly from your terminal, compile it to a standalone .exe yourself (using PyInstaller), or just paste the code into an online Python compiler like runcell.

Below is the raw XYZ coordinate data I mapped for the tool. I am posting it here so other developers and spreadsheet nerds can easily find and index the Pyro terminal locations:

Star Citizen Pyro System 3D Terminal Coordinates (XYZ):

{
    "Ashland [Ignis]": [-3965622221.89, -42774737330.02, 265976.59],
    "Bueno Ravine [Bloom]": [-16104019781.39, 7587544650.78, -55043.01],
    "Canard View [Terminus]": [-48118473390.85, 48503311205.01, -104210.22],
    "Chawla's Beach [Pyro IV]": [-3704202834.72, -43071407531.14, 189342.61],
    "Checkmate [PYR2-L4]": [10550863484.67, -959299359.50, 2571755.75],
    "Dudley & Daughters [PYR6-L4]": [-62062402883.67, -28602577028.15, -10764520.96],
    "Endgame [PYR6-L3]": [52140944773.96, -44113647219.89, 2734498.95],
    "Fallow Field [Pyro IV]": [-3704313644.11, -43071728311.58, 262110.71],
    "Gaslight [PYR5-L2]": [-4296211488.20, -47014546578.03, 17666075.21],
    "Jackson's Swap [Monox]": [6056859904.66, -8725134703.79, -273224.84],
    "Last Landings [Terminus]": [-48118047765.14, 48503305155.67, -7951.43],
    "Megumi [PYR6-L5]": [29108873984.26, 61780158835.53, 12706257.47],
    "Nyx Gateway [Pyro]": [-35275902938.53, -36052293448.74, 5231.81],
    "Orbituary [Bloom Orbit]": [-16104236039.34, 7587206482.42, 224851.09],
    "Patch City [PYR3-L3]": [14526918709.54, -10272844456.63, 2753405.01],
    "Rat's Nest [PYR5-L5]": [-42308388155.28, -7276095571.88, 18512845.94],
    "Rod's Fuel [PYR5-L4]": [41418349978.46, -11259078248.60, -9517934.04],
    "Ruin Station [Pyro VI]": [-48117838232.50, 48503143589.65, -100441.22],
    "Rustville [Pyro I]": [-7920868603.01, -2387347665.59, 171785.01],
    "Sacren's Plot [Pyro IV]": [-3703973430.71, -43071855333.72, 195716.17],
    "Scarper's Turn [Terminus]": [-48118037051.47, 48503290214.47, 233.23],
    "Seer's Canyon [Vatra]": [-3889610903.51, -42688332205.25, -119699.37],
    "Shepherd's Rest [Bloom]": [-16104122890.37, 7588219046.93, -202689.27],
    "Stanton Gateway [Pyro]": [34178594701.87, 40592016062.75, 5998.74],
    "Starlight Service [PYR3-L1]": [-14605356844.76, 6564827390.36, 1357943.13],
    "Sunset Mesa [Monox]": [6057708176.97, -8724811417.02, -272094.59],
    "The Golden Riviera [Bloom]": [-16104266443.96, 7587755523.50, 5201.75]
}
