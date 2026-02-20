# Clothing & Texture System — WebRTC Command Reference

This document covers the WebRTC command protocol for changing clothing and textures on MetaHuman characters via Pixel Streaming. Each texture folder in this repository is mapped to its corresponding command.

Developers adapting this to their own project should treat the command formats below as a protocol specification and the texture folders as UV painting references.

---

## Command Types

All commands are sent as strings over the WebRTC DataChannel via Pixel Streaming's `emitCommand({ command: '...' })` interface.

### `PRS.` — Character Preset
Loads a base MetaHuman. Must be sent first; all other commands layer on top.
```
PRS.Fem       // Lucy
PRS.Fem1      // Sarah (Mai)
PRS.Masc1     // Harry
PRS.Masc      // Zach
```

### `OF_` — Outfit Equip
Equips or removes a clothing mesh on a slot.
```
OF_<ManifestValue>
```
Example: `OF_Tops_Lucy.SleevelessDress`

### `OFC_` — Outfit Skin (Parametric Texture Swap)
Swaps Diffuse/Normal/SRMF textures on a currently equipped clothing mesh. Only works on clothing types that have registered skins.
```
OFC_<ClothingCategory>_<ClothingType>.<TextureName>
```
Example: `OFC_Tops_SleevelessDress.BWShred`

### `OFCD_` — Custom Texture via URL
Sends a public image URL to Unreal Engine, which downloads and applies it as a dynamic texture. Used for AI-generated or user-drawn textures on face, body, or clothing.
```
OFCD_<ObjectType>_<PublicImageURL>
```
Object types: `Face`, `Body`, `Legs` (more can be added per Blueprint setup).

### `SKC.` — Skin Color
Sets the skin tone. Value range: `0.04` to `1.30`.
```
SKC.<float>
```
Example: `SKC.0.67`

### `PLAYER_` — Player Controller Setup
Sent after all customization commands to initialize the player controller.
```
PLAYER_Metahuman
```

---

## Outfit Slots

| Slot  | Manifest Category | None Value       |
|-------|-------------------|------------------|
| Head  | Headwear          | `Headwear_None`  |
| Torso | Tops              | `Tops_Top_None`  |
| Legs  | Legs              | `Bottoms_None`   |
| Feet  | Feet              | `Shoes_None`     |
| Back  | Magic             | `Magic_None`     |

---

## Smart Pairing Rules

When certain torso items are equipped, legs should auto-pair:

| Torso Type           | Auto-Set Legs To                     | Reason                          |
|----------------------|--------------------------------------|---------------------------------|
| SleevelessDress      | `Bottoms_None`                       | Dress covers legs               |
| TurtleNeckSweater    | `Bottoms_<Character>.Skirt`          | Designed as a combo             |
| TShirt               | `Bottoms_<Character>.CargoPant`      | Casual pairing                  |
| TripleLayerVest      | `Bottoms_<Character>.Shorts`         | Outdoor pairing                 |

---

## Command Sequence Order

When loading a full character, commands are sent in this order:

1. `PRS.*` — Character preset (sent first, with delay before rest)
2. `SKC.*` — Skin color
3. `EYE_*` — Eye parameters
4. `MT_*` — Face morph targets
5. `MKUPF_*` / `MKUPC_*` — Makeup (feminine characters only)
6. `HS_*` — Hairstyle + `HAIRF_*` / `HCR.` / `HCG.` / `HCB.` hair params
7. `OF_*` — Clothing (outfit values sent raw, e.g. `Tops_Lucy.TurtleNeckSweater`)
8. Animation commands
9. `OFCD_*` — Custom skin textures (face/body URLs)
10. `PLAYER_Metahuman` — Sent last after a delay

---

## Texture Folders & Example WebRTC Commands

### MetaHuman Face Textures

Face textures are unique per character. They are loaded as part of the character preset (`PRS.*`), or can be overridden dynamically with `OFCD_Face_`.

#### MetaHuman-1-Lucy / Face
**Path:** `MetaHumans/MetaHuman-1-Lucy/Face/`
**Files:** `T_Head_BC.PNG`, `T_Head_N.PNG`, `T_Head_SRMF.PNG`

Load Lucy (which applies her default face texture):
```
PRS.Fem
```
Override with a custom face texture from a URL:
```
OFCD_Face_https://example.com/my_custom_face.png
```

---

#### MetaHuman-2-Sarah / Face
**Path:** `MetaHumans/MetaHuman-2-Sarah/Face/`
**Files:** `T_Head_BC.PNG`, `T_Head_N.PNG`, `T_Head_SRMF.PNG`

Load Sarah:
```
PRS.Fem1
```
Override with a custom face texture:
```
OFCD_Face_https://example.com/my_custom_face.png
```

---

#### MetaHuman-3-Harry / Face
**Path:** `MetaHumans/MetaHuman-3-Harry/Face/`
**Files:** `T_Head_BC.PNG`, `T_Head_N.PNG`, `T_Head_SRMF.PNG`

Load Harry:
```
PRS.Masc1
```
Override with a custom face texture:
```
OFCD_Face_https://example.com/my_custom_face.png
```

---

#### MetaHuman-4-Zach / Face
**Path:** `MetaHumans/MetaHuman-4-Zach/Face/`
**Files:** `T_Head_BC.PNG`, `T_Head_N.PNG`, `T_Head_SRMF.PNG`

Load Zach:
```
PRS.Masc
```
Override with a custom face texture:
```
OFCD_Face_https://example.com/my_custom_face.png
```

---

### MetaHuman Body Textures

Body textures are also unique per character and loaded via the preset. Override dynamically with `OFCD_Body_`.

#### MetaHuman-1-Lucy / Body
**Path:** `MetaHumans/MetaHuman-1-Lucy/Body/`
**Files:** `T_Body_BC.PNG`, `T_Body_N.PNG`, `T_Body_Scatter.PNG`, `T_Body_SRMF.PNG`

Load Lucy (applies default body):
```
PRS.Fem
```
Override with a custom body texture:
```
OFCD_Body_https://example.com/my_custom_body.png
```

---

#### MetaHuman-2-Sarah / Body
**Path:** `MetaHumans/MetaHuman-2-Sarah/Body/`
**Files:** `T_Body_BC.PNG`, `T_Body_N.PNG`, `T_Body_SRMF.PNG`

Load Sarah:
```
PRS.Fem1
```
Override:
```
OFCD_Body_https://example.com/my_custom_body.png
```

---

#### MetaHuman-3-Harry / Body
**Path:** `MetaHumans/MetaHuman-3-Harry/Body/`
**Files:** `T_Body_BC.PNG`, `T_Body_N.PNG`, `T_Body_SRMF.PNG`

Load Harry:
```
PRS.Masc1
```
Override:
```
OFCD_Body_https://example.com/my_custom_body.png
```

---

#### MetaHuman-4-Zach / Body
**Path:** `MetaHumans/MetaHuman-4-Zach/Body/`
**Files:** `T_Body_BC.PNG`, `T_Body_N.PNG`, `T_Body_SRMF.PNG`

Load Zach:
```
PRS.Masc
```
Override:
```
OFCD_Body_https://example.com/my_custom_body.png
```

---

### Clothing — Torso

#### Sleeveless Dress
**Path:** `Clothing/MetaHuman-1-Lucy/Torso/Sleeveless-Dress/`
**Files:** `LucyDressSleeveless_Diffuse_BWShred.PNG`, `LucyDressSleeveless_Normal_BWShred.PNG`, `LucyDressSleeveless_SRMF_BWShred.PNG`
**Note:** Auto-sets legs to `Bottoms_None` (dress covers legs).

Equip:
```
OF_Tops_Lucy.SleevelessDress
```
Remove legs (sent automatically):
```
OF_Bottoms_None
```
Apply a skin:
```
OFC_Tops_SleevelessDress.BWShred
```

Available skins: `Default`, `BeigeWoven`, `BlueClouds`, `BWFerro`, `BWShred`, `BWStudded`, `CarbonFiber`, `GreenGrid`, `GreenNet`, `GreenSquared`, `GreyFeathers`, `GreyFelt`, `LinenBeige`, `Phoenix`, `Polkadot`, `RedNylon`, `RipstopFloral`, `RoyalGreen`, `RoyalPink`, `SalmonCotton`

---

#### T-Shirt
**Path:** `Clothing/MetaHuman-1-Lucy/Torso/T-Shirt/`
**Files:** `T-Shirt_PlainWhite_BC.PNG`, `T-Shirt_PlainWhiteN.PNG`, `T-Shirt_PlainWhiteSRMF.PNG`
**Note:** Auto-pairs with CargoPant.

Equip:
```
OF_Tops_Lucy.TShirt
```
Auto-set legs (sent automatically):
```
OF_Bottoms_Lucy.CargoPant
```
Apply a skin:
```
OFC_Tops_TShirt.PlainWhite
```

Available skins: `Default`, `BeigeWash`, `NavyWhiteStripe`, `PinkCamo`, `PinkHexLogo`, `Starlight`, `ShadedSquare`, `RWFlannel`, `GreyLogo`, `Grey`, `GreenStripeLogo`, `GreenStripe`, `BlueDiamond`, `BlackLogo`, `PlainWhiteLogo`, `PlainWhite`

---

#### Turtleneck Sweater Combo
**Path:** `Clothing/MetaHuman-1-Lucy/Torso/Turtleneck-Sweater-Combo/`
**Note:** Two-piece outfit (Turtleneck + Jacket) with separate element IDs (1 = Turtleneck, 2 = Jacket). Auto-pairs with Skirt.

**Turtleneck sub-folder:** `TurtleNeck_TurtleNeck_BaseColor.PNG`, `TurtleNeck_TurtleNeck_Normal.PNG`, `TurtleNeck_TurtleNeck_OcclusionRoughnessMetallic.PNG`
**Sweater sub-folder:** `Jacket_Jacket_BaseColor.PNG`, `Jacket_Jacket_Normal.PNG`, `Jacket_Jacket_OcclusionRoughnessMetallic.PNG`

Equip:
```
OF_Tops_Lucy.TurtleNeckSweater
```
Auto-set legs (sent automatically):
```
OF_Bottoms_Lucy.Skirt
```

---

#### Layered Outdoor Jacket (Triple Layer Vest)
**Path:** `Clothing/MetaHuman-1-Lucy/Torso/Layered-Outdoor-Jacket/`
**Files:** `TripleLayerJacketLSTee_normal.PNG`, `TX_TripleLayerVestDiffuse.PNG`
**Note:** Auto-pairs with Shorts.

Equip:
```
OF_Tops_Lucy.TripleLayerVest
```
Auto-set legs (sent automatically):
```
OF_Bottoms_Lucy.Shorts
```

---

#### Embody Puffer
**Path:** `Clothing/MetaHuman-1-Lucy/Torso/Embody-Puffer/`
**Files:** `embody_pufferbase_color_texture.PNG`
**Note:** Set legs to none. This item is not in the current clothing manifest — it requires a custom manifest entry to equip via `OF_`. The texture here is the UV reference for adding it.

To add it, register in your manifest under `[Tops]` and equip via:
```
OF_Tops_Lucy.EmbodyPuffer
```
Then set:
```
OF_Bottoms_None
```

---

#### Livepeer Outfit
**Path:** `Clothing/MetaHuman-1-Lucy/Torso/Livepeer-Outfit/`
**Files:** `livepeerlucyfinaldraftv1_diffuse.PNG`
**Note:** Full-body outfit. Set legs and feet to none. Not in the current clothing manifest — requires a custom entry.

To add it, register in your manifest and equip via:
```
OF_Tops_Lucy.LivepeerOutfit
```
Then clear lower body:
```
OF_Bottoms_None
OF_Shoes_None
```

---

#### Pumpkin Witch
**Path:** `Clothing/MetaHuman-1-Lucy/Torso/Pumpkin-Witch/`
**Files:** `pumpkin_witch_textures_diffuse.PNG`, `pumpkin_witch_textures_normal.PNG`
**Note:** Set legs to none. Not in the current clothing manifest — requires a custom entry.

To add it, register in your manifest and equip via:
```
OF_Tops_Lucy.PumpkinWitch
```
Then set:
```
OF_Bottoms_None
```

---

### Clothing — Legs

#### Cargo Pants
**Path:** `Clothing/MetaHuman-1-Lucy/Legs/Cargo-Pants/`
**Files:** `T_CargoPants_BWCamo_BC.PNG`, `T_CargoPants_BWCamo_N.PNG`, `T_CargoPants_BWCamo_ORM.PNG`

Equip:
```
OF_Bottoms_Lucy.CargoPant
```
Apply a skin:
```
OFC_Bottoms_CargoPant.BWCamo
```

Available skins: `Default`, `AfghanCamo`, `BWCamo`, `BWHex`, `CaddyFlannel`, `Dart`, `FullCamo`, `PinkCamo`, `PinkHearts`, `PinkPlad`, `RBFlannel`, `RWFlannel`, `WHex`, `WhiteoutHex`, `WOHex`, `YellowThread`

---

#### Skater Shorts
**Path:** `Clothing/MetaHuman-1-Lucy/Legs/Skater-Shorts/`
**Files:** `Lucy_Shorts_BC.PNG`

Equip:
```
OF_Bottoms_Lucy.Shorts
```

---

#### Skirt
**Path:** `Clothing/MetaHuman-1-Lucy/Legs/Skirt/`
**Files:** `Skirt_Skirt_BaseColor.PNG`, `Skirt_Skirt_Normal.PNG`, `Skirt_Skirt_OcclusionRoughnessMetallic.PNG`

Equip:
```
OF_Bottoms_Lucy.Skirt
```
Apply a skin:
```
OFC_Legs_Skirt.Tiger
```
Apply a custom texture via URL:
```
OFCD_Legs_https://example.com/my_custom_skirt.png
```

Available skins: `Default`, `Tiger`, `NavyLint`, `BlueFloral`, `RedFloral`

---

### Clothing — Feet

#### Heels
**Path:** `Clothing/MetaHuman-1-Lucy/Feet/Heels/`
**Files:** `HighHeels_GreenCasual_BC.PNG`, `HighHeels_GreenCasual_N.PNG`, `HighHeels_GreenCasual_SRMF.PNG`

Equip:
```
OF_Shoes_Lucy.Heels
```

---

#### Sneakers
**Path:** `Clothing/MetaHuman-1-Lucy/Feet/Sneakers/`
**Files:** `Sneakers_BW_BC.PNG`, `Sneakers_BW_N.PNG`, `Sneakers_BW_SRMF.PNG`

Equip:
```
OF_Shoes_Lucy.Sneakers
```

---

### Clothing — Head

**Path:** `Clothing/MetaHuman-1-Lucy/Head/`
**Note:** No textures yet. Headwear slot is available in the manifest.

Remove headwear:
```
OF_Headwear_None
```

---

### Clothing — Back

**Path:** `Clothing/MetaHuman-1-Lucy/Back/`
**Note:** No textures yet. Magic slot is available.

Equip wings:
```
OF_Magic_Wings
```
Remove:
```
OF_Magic_None
```

---

## Texture Map Naming Conventions

| Suffix       | Meaning                                |
|-------------|----------------------------------------|
| `_BC`       | Base Color (Diffuse)                   |
| `_N`        | Normal Map                             |
| `_SRMF`    | Specular, Roughness, Metallic, Fuzz    |
| `_ORM`     | Occlusion, Roughness, Metallic         |
| `_Scatter` | Subsurface Scattering                  |
| `_BaseColor` | Base Color (alternate naming)        |
| `_Normal`   | Normal Map (alternate naming)         |
| `_OcclusionRoughnessMetallic` | Combined ORM map |
| `_diffuse`  | Diffuse/Base Color (alternate naming) |
| `_normal`   | Normal Map (alternate naming, lowercase) |

---

## Character Substitution

All `OF_` commands use a character name token. Replace `Lucy` with the target:

| Character | Token   | Preset Command |
|-----------|---------|----------------|
| Lucy      | `Lucy`  | `PRS.Fem`      |
| Sarah     | `Sarah` | `PRS.Fem1`     |
| Harry     | `Harry` | `PRS.Masc1`    |
| Zach      | `Zach`  | `PRS.Masc`     |

Example — equip Sneakers on Harry:
```
OF_Shoes_Harry.Sneakers
```

Not all clothing items are available for all characters. The manifest defines availability:
- SleevelessDress: Lucy, Sarah
- TurtleNeckSweater: Lucy, Sarah, Harry
- TShirt: All four
- TripleLayerVest: All four
- Skirt: Lucy, Sarah
- CargoPant: All four
- Shorts: All four
- AthleticShortsF: Lucy, Sarah
- Sneakers: All four
- Heels: Lucy, Sarah

---

## Adding New Textures

1. Paint over the UV map PNG in the appropriate folder
2. Name textures following the convention: `<ClothingName>_<SkinName>_BC.PNG`, `_N.PNG`, `_SRMF.PNG`
3. Register the skin in the clothing manifest under `[Skins]` as `ClothingType.SkinName`
4. The command `OFC_<Category>_<ClothingType>.<SkinName>` will work once registered

To add an entirely new clothing item, you need Unreal-side Blueprint/mesh setup and a new manifest entry under the appropriate `[Category]` section.
