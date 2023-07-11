<script lang="ts">
  const rollableStart = `[rollable]`;
  const rollableEnd = `[/rollable]`;
  let displayTextPristine = true;

  const rollableData = {
    displayText: '1d8+3',
    diceNotation: '1d8+3',
    rollType: 'roll',
    rollAction: undefined,
    rollDamageType: undefined
  };

  function displayTextUpdated(): void {
    displayTextPristine = false;
    const displayTextInput = document.getElementById("displayText") as HTMLInputElement;
    rollableData.displayText = displayTextInput.value;
  }

  function diceRollUpdated(): void {
    const diceNotationInput = document.getElementById("diceNotation") as HTMLInputElement;
    rollableData.diceNotation = diceNotationInput.value;

    if (displayTextPristine) {
      rollableData.displayText = rollableData.diceNotation;
    }
  }

  function rollTypeUpdated(): void {
    const rollTypeInput = document.getElementById('rollType') as HTMLSelectElement;
    const rollType = rollTypeInput.value;
    rollableData.rollType = rollType;

    if (isToHitRoll) {
      rollableData.diceNotation = '1d20'
    }
  }
  function rollActionUpdated(): void {
    const rollActionInput = document.getElementById('rollAction') as HTMLInputElement;
    rollableData.rollAction = rollActionInput.value;
  }
  function rollDamageTypeUpdated(): void {
    const rollDamageTypeInput = document.getElementById('rollDamageType') as HTMLSelectElement;
    rollableData.rollDamageType = rollDamageTypeInput.value;
  }

  function buildRollableTag({displayText, ...rollData}): string {
    const jsonPart = JSON.stringify(rollData);
    return `${rollableStart} ${displayText}; ${jsonPart} ${rollableEnd}`
  }

  $: rollableTag = buildRollableTag(rollableData);
  $: damageTypeInputEnabled = rollableData.rollType == 'damage' || rollableData.rollType == 'spell';
  $: isToHitRoll = rollableData.rollType == 'to hit';

  async function copy() {
    await navigator.clipboard.writeText(rollableTag);
  }

</script>

<main>  
  <h1>D&DBeyond Rollable Tag Maker</h1>

  <form id="tagMakerForm">
    <fieldset>
      <label for="rollType">Roll Type</label>
      <select bind:value={rollableData.rollType} id="rollType" on:change={rollTypeUpdated}>
        <option value="roll" title="used for a generic dice roll.">roll</option>
        <option value="to hit" title="used for attack rolls.">to hit</option>
        <option value="damage" title="used for damage rolls. Should include rollDamageType when using this.">damage</option>
        <option value="heal" title="used for healing.">heal</option> 
        <option value="spell" title="can be used to differentiate a spell attack from a regular attack roll.">spell</option>
        <option value="save" title="used for saving throws.">save</option> 
        <option value="check" title="used for profiency checks (skills)">check</option>
      </select>
      
      <label for="diceNotation">Dice Roll (1d8+3)</label>
      <input bind:value={rollableData.diceNotation} on:keyup={diceRollUpdated} id="diceNotation"/>
      
      <label for="displayText">Display Text</label>
      <input bind:value={rollableData.displayText} on:keyup={displayTextUpdated} id="displayText"/>

      <label for="rollAction">Roll Action (Optional)</label>
      <input bind:value={rollableData.rollAction} on:keyup={rollActionUpdated} id="rollAction" placeholder="{rollableData.displayText}" />

      <label for="rollDamageType">Damage Type</label>
      <select disabled={!damageTypeInputEnabled} bind:value={rollableData.rollDamageType} on:change={rollDamageTypeUpdated} id="rollDamageType">
        <option value="">None</option>
        <option value="Bludgeoning">Bludgeoning</option>
        <option value="Acid">Acid</option>
        <option value="Cold">Cold</option>
        <option value="Fire">Fire</option>
        <option value="Force">Force</option>
        <option value="Lightning">Lightning</option>
        <option value="Necrotic">Necrotic</option>
        <option value="Piercing">Piercing</option>
        <option value="Poison">Poison</option>
        <option value="Psychic">Psychic</option>
        <option value="Radiant">Radiant</option>
        <option value="Slashing">Slashing</option>
        <option value="Thunder">Thunder</option>
        <option></option>
      </select>
    </fieldset>
  </form>
  
  <br/>

  <textarea id="rollableTagArea">{rollableTag}</textarea>

  <br />
  <button on:click={copy}>Copy</button>
</main>

<style>

#rollableTagArea {
  min-width: 300px;
  min-height: 150px;
}

#tagMakerForm > fieldset {
  display: flex;
  flex-flow: column;
  text-align: left;
}

</style>
