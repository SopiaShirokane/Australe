import discord
import json
import ast
import requests
from discord.ext import commands
import re

def apiGet(route, fileName):
    response = requests.get(f"https://api.ennead.cc/stella/{route}")

    with open(fileName, "w", encoding="utf-8") as f:
        print(response.json(), file=f)
    with open(fileName, "r", encoding="utf-8") as f:
        fileData = ast.literal_eval(f.read())
    with open(fileName, "w", encoding="utf-8") as f:
        json.dump(fileData, f, ensure_ascii=False, indent=2)
    with open(fileName, "r", encoding="utf-8") as f:
        fileData = json.load(f)
    return fileData

def trim(text, limit):
    text = str(text)
    return text if len(text) <= limit else text[:limit - 3] + "..."

def fillParams(text, params, level):
    def sub(m):
        parts = params[int(m.group(1)) - 1].split("/")
        return parts[level] if level < len(parts) else parts[-1]
    return re.sub(r"&Param(\d+)&", sub, text)

def clean(text):
    text = text.replace("\u000b", "\n")
    text = re.sub(r"<color=#[0-9a-fA-F]+>(.*?)</color>", r"**\1**", text)
    text = re.sub(r"##([^#]+)#\d+#", r"\1", text)
    return text

def skillEmbed(charData):
    level = 10
    descN = clean(fillParams(charData["normalAttack"]["description"], charData["normalAttack"]["params"], level))
    descS = clean(fillParams(charData["skill"]["description"], charData["skill"]["params"], level))
    descSP = clean(fillParams(charData["supportSkill"]["description"], charData["supportSkill"]["params"], level))
    descU = clean(fillParams(charData["ultimate"]["description"], charData["ultimate"]["params"], level))
    
    embed = discord.Embed(title="Skills", color=0x00caff)
    embed.set_thumbnail(url=f"https://api.ennead.cc/{charData['icon']}")
    embed.add_field(name=f'Normal Attack: {charData["normalAttack"]["name"]}', inline=False, value=trim(descN, 4096))
    embed.add_field(name=f'Main Skill: {charData["skill"]["name"]}', inline=False, value=trim(descS, 4096))    
    embed.add_field(name=f'Support Skill: {charData["supportSkill"]["name"]}', inline=False, value=trim(descSP, 4096))    
    embed.add_field(name=f'Ultimate: {charData["ultimate"]["name"]}', inline=False, value=trim(descU, 4096))    

    #for hint in charData["normalAttack"].get("hints", {}).values():
    #    hintDesc = clean(fillParams(hint["description"], hint["params"], 0))
    #    embed.add_field(name=trim(hint["name"], 256), value=trim(hintDesc, 1024), inline=False)

    return embed

def potMainEmbed(charData):
    level = 9
    embed = discord.Embed(title="Main & Common Potentials", color=0x00caff)
    embed.set_thumbnail(url=f"https://api.ennead.cc/{charData['icon']}")
    embed.add_field(name="Main Core", value="", inline=False)
    for pot in charData["potentials"]["mainCore"]:
        desc = clean(fillParams(pot["description"], pot["params"], level))
        embed.add_field(name=pot["name"], inline=False, value=trim(desc, 4096))
    embed.add_field(name="Main", value="", inline=False)
    for pot in charData["potentials"]["mainNormal"]:
        desc = clean(fillParams(pot["description"], pot["params"], level))
        embed.add_field(name=pot["name"], inline=False, value=trim(desc, 4096))
    embed.add_field(name="Common", value="", inline=False)
    for pot in charData["potentials"]["common"]:
        desc = clean(fillParams(pot["description"], pot["params"], level))
        embed.add_field(name=pot["name"], inline=False, value=trim(desc, 4096))

    return embed

def potSupportEmbed(charData):
    level = 9
    embed = discord.Embed(title="Support Potentials", color=0x00caff)
    embed.set_thumbnail(url=f"https://api.ennead.cc/{charData['icon']}")
    embed.add_field(name="Support Core", value="", inline=False)
    for pot in charData["potentials"]["supportCore"]:
        desc = clean(fillParams(pot["description"], pot["params"], level))
        embed.add_field(name=pot["name"], inline=False, value=trim(desc, 4096))
    embed.add_field(name="Support", value="", inline=False)
    for pot in charData["potentials"]["supportNormal"]:
        desc = clean(fillParams(pot["description"], pot["params"], level))
        embed.add_field(name=pot["name"], inline=False, value=trim(desc, 4096))

    return embed

intents = discord.Intents.default()
intents.message_content = True
bot = commands.Bot(command_prefix="a!", intents=intents, help_command=None)

@bot.event
async def on_ready():
    print(f"Logged in as {bot.user}")

@bot.command(name="char", aliases=["c"])
async def char(ctx, name):
    charData = apiGet(f"character/{name}", "character.json")
    if charData["grade"] == 4:
        embed = discord.Embed(title=f'{charData["name"]} \n⭐⭐⭐⭐', color=0x00caff, description=charData["description"])
    else:
        embed = discord.Embed(title=f'{charData["name"]} \n⭐⭐⭐⭐⭐', color=0x00caff, description=charData["description"])
    embed.set_thumbnail(url=f"https://api.ennead.cc/{charData["icon"]}")
    if charData["element"] == "Lux":
        embed.add_field(name="Element", inline=True, value=f'<:lux_icon:1563580838904471653> {charData["element"]}')
    elif charData["element"] == "Ignis":
        embed.add_field(name="Element", inline=True, value=f'<:ignis_icon:1563580795971702794> {charData["element"]}')
    elif charData["element"] == "Ventus":
        embed.add_field(name="Element", inline=True, value=f'<:ventus_icon:1563580824404627688> {charData["element"]}')
    elif charData["element"] == "Terra":
        embed.add_field(name="Element", inline=True, value=f'<:terra_icon:1563580810316095601> {charData["element"]}')
    elif charData["element"] == "Aqua":
        embed.add_field(name="Element", inline=True, value=f'<:aqua_icon:1563580776854065374> {charData["element"]}')
    else:
        embed.add_field(name="Element", inline=True, value=f'<:umbra_icon:1563580854834561044> {charData["element"]}')
    embed.add_field(name="Position", inline=True, value=charData["position"])
    embed.add_field(name="Attack type", inline=True, value=charData["attackType"])
    embed.add_field(name="Style", inline=True, value=charData["style"])
    embed.add_field(name="Faction", inline=True, value=charData["faction"])
    embed.add_field(name="Stats (at level 90)", inline=True, value=f"HP: {charData["stats"][len(charData["stats"])-1]["hp"]} \nATK: {charData["stats"][len(charData["stats"])-1]["atk"]}")
    embed.add_field(name="Talents", inline=False, value="")
    level = 0
    for talent in charData["talents"]:
        desc = clean(fillParams(talent["talents"][0]["description"], talent["talents"][0]["params"], level))
        embed.add_field(name=talent["name"], inline=False, value=trim(desc, 4096))
    
    class charSkills(discord.ui.View):
        def __init__(self):
            super().__init__(timeout=60)
        @discord.ui.button(label="Skills", style=discord.ButtonStyle.primary)
        async def charSkill(self, interaction=discord.Interaction, button=discord.ui.Button):
            await interaction.response.edit_message(embed=skillEmbed(charData), view=charSkills())

    class charNormal(discord.ui.View):
        def __init__(self):
            super().__init__(timeout=60)
        @discord.ui.button(label="Character information", style=discord.ButtonStyle.primary)
        async def charSkill(self, interaction=discord.Interaction, button=discord.ui.Button):
            charInfoEmbed = discord.Embed(title=charData["name"], color=0x00caff)
            charInfoEmbed.set_thumbnail(url=f"https://api.ennead.cc/{charData['icon']}")
            if charData["grade"] == 4:
                charInfoEmbed = discord.Embed(title=f'{charData["name"]} \n⭐⭐⭐⭐', color=0x00caff, description=charData["description"])
            else:
                charInfoEmbed = discord.Embed(title=f'{charData["name"]} \n⭐⭐⭐⭐⭐', color=0x00caff, description=charData["description"])
                charInfoEmbed.set_thumbnail(url=f"https://api.ennead.cc/{charData["icon"]}")
                charInfoEmbed.add_field(name="Element", inline=True, value=charData["element"])
                charInfoEmbed.add_field(name="Position", inline=True, value=charData["position"])
                charInfoEmbed.add_field(name="Attack type", inline=True, value=charData["attackType"])
                charInfoEmbed.add_field(name="Style", inline=True, value=charData["style"])
                charInfoEmbed.add_field(name="Faction", inline=True, value=charData["faction"])
                charInfoEmbed.add_field(name="Stats (at level 90)", inline=True, value=f"HP: {charData["stats"][len(charData["stats"])-1]["hp"]} \nATK: {charData["stats"][len(charData["stats"])-1]["atk"]}")
            await interaction.response.edit_message(embed=charInfoEmbed, view=charSkills())


    class charPots(discord.ui.View):
        def __init__(self):
            super().__init__(timeout=60)
        @discord.ui.button(label="Potentials", style=discord.ButtonStyle.primary)
        async def charPot(self, interaction=discord.Interaction, button=discord.ui.Button):
            await interaction.response.edit_message(embed=potEmbed(charData), view=charNormal())
   
    await ctx.send(embed=embed, view=charSkills())

@bot.command(name="potential", aliases=["p"])
async def potential(ctx, name):
    charData = apiGet(f"character/{name}", "character.json")
    class charMainPots(discord.ui.View):
        def __init__(self):
            super().__init__(timeout=60)
        @discord.ui.button(label="Main & Common Potentials", style=discord.ButtonStyle.primary)
        async def charPot(self, interaction=discord.Interaction, button=discord.ui.Button):
            await interaction.response.edit_message(embed=potMainEmbed(charData), view=charSupportPots())

    class charSupportPots(discord.ui.View):
        def __init__(self):
            super().__init__(timeout=60)
        @discord.ui.button(label="Support Potentials", style=discord.ButtonStyle.primary)
        async def charPot(self, interaction=discord.Interaction, button=discord.ui.Button):
            await interaction.response.edit_message(embed=potSupportEmbed(charData), view=charMainPots())
    await ctx.send(embed=potMainEmbed(charData), view=charSupportPots())

bot.run("")
