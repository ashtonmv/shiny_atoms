# POV-Ray Atoms

## Install POV-Ray

Mac (via Homebrew): `brew install povray`

Windows: download and run the installer from [here](https://www.povray.org/download/)

## Install Ovito
Download the version for your computer [here](https://www.povray.org/download/)

## Open the structure in Ovito and manipulate to the scene
The most important part is getting the view angle you want, but ovito also makes it really easy to make a supercell, change atom colors, remove cell boundaries, add bonds, etc.

## Export POV-Ray file
file > Export File > select POV-Ray scene

## shinify the .pov file (something like this.. sorry for the weak code):

```python
def shinify(input_filename, output_filename, specular=0.9, reflection=0.6, diffuse=0.4):

    lines = open(input_filename).readlines()
    with open(output_filename, "w") as f:
        i = 0
        while i < len(ovito_lines):
            line = ovito_lines[i]

            if line.split()[0] == "translate":
                light_x, light_y, light_z = line.split("<")[1].split(">")[0].split(",")
            
            # Add better lighting
            if line.split()[0] == "light_source":
                f.write(
                    "light_source {\n"
                    + f"  <{light_x}, {light_y}, {light_z}>\n"
                    + "  color <1, 1, 1>\n"
                    + "}\n"
                )
                i += 6

            # Add shiny finish to spherical atoms
            elif line[0:14] == "#macro SPRTCLE":
                f.write(
                    "#macro SPRTCLE(pos, particleRadius, particleColor, spc, ref, dif) // Macro for spherical particles\n"
                    + "sphere { pos, particleRadius\n"
                    + "         texture { pigment { color particleColor } finish { specular spc reflection ref diffuse dif } }\n"
                    + "}\n#end\n"
                )
                i += 4
            elif line[0:7] == "SPRTCLE":
                f.write(line.replace(")", f", {specular}, {reflection}, {diffuse})"))
            
            # Add shiny finish to cylindrical bonds
            elif line[0:10] == "#macro CYL":
                f.write(
                    "#macro CYL(base, dir, cylRadius, cylColor) // Macro for cylinders"
                    + "cylinder { base, base + dir, cylRadius"
                    + "         texture { pigment { color cylColor } finish { specular spc reflection ref diffuse dif } }\n"
                    + "}\n#end\n"
                )
                i += 4
            elif line[0:4] == "CYL":
                f.write(line.replace(")", f", {specular}, {reflection}, {diffuse})"))
            else:
                f.write(line)
            i += 1

shinify("ovito_file.pov", "ovito_file_shiny.pov")
```

## Finally, run `povray` on the new shiny .pov file
