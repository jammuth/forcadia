<template>
  <div class="home">
    <div class="list-metadata">
      <label for="listname">Enter List Name: </label>
      <input type="text" name="listname" id="listname" minlength="4" />
      <button class="btn">Save List</button>
    </div>

    <div class="unit-select">
      <select name="role-select" v-model="selectedRole" @change="getUnits">
        <option v-for="roleList in rolesList" :value="roleList">
          {{ roleList }}
        </option>
      </select>
      <select name="unit-select" v-model="selectedUnit">
        <option v-for="unitList in unitsList.units" :value="unitList">
          {{ unitList }}
        </option>
      </select>
      <button class="btn" @click="addUnit">Add Unit</button>
    </div>

    <div class="armylist">
      <div class="unit" v-for="unit in myUnits.units" :key="unit.id">
        {{
          `${unit.name.padEnd(50, ".")} +${unit.cost
            .toString()
            .padStart(2, "0")} pts`
        }}
        <div v-for="extra in unit.extras">
          {{ extra.name }}

          <!--check-->
          <div
            v-if="extra.type == 'check'"
            v-for="option in extra.options"
            :key="option.id"
          >
            <input v-model="option.active" type="checkbox" />
            {{
              `${option.name.padEnd(30, ".")} +${option.cost
                .toString()
                .padStart(2, "0")} pts`
            }}
          </div>

          <!--group-->
          <div
            v-if="extra.type == 'group'"
            v-for="option in extra.options"
            :key="option.id"
          >
            <input
              v-model="option.active"
              type="radio"
              :name="unit.id + ' ' + extra.id"
            />
            {{
              `${option.name.padEnd(30, ".")} +${option.cost
                .toString()
                .padStart(2, "0")} pts`
            }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from "vue";
import pointsData from "../assets/points_list.json";

const rolesList = ref(
  pointsData
    .map((val) => val.role)
    .filter((v, i, s) => {
      return s.indexOf(v) === i;
    })
);

const selectedRole = ref(rolesList.value[0]);

const unitsList = reactive({ units: [] });
const myUnits = reactive({ units: [], counter: 0 });

const getUnits = () => {
  unitsList.units = pointsData
    .filter((el) => el.role == selectedRole.value)
    .map((e) => e.name);
  selectedUnit.value = unitsList.units[0];
};

const getUnit = (name) => {
  return pointsData.filter((el) => el.name == name)[0];
};

const selectedUnit = ref(unitsList.units[0]);
getUnits();

const addUnit = () => {
  const unit = getUnit(selectedUnit.value);
  unit.id = ++myUnits.counter;
  myUnits.units.push(unit);
};
</script>

<style>
.home {
  width: 100%;
  text-align: left;
}
select {
  margin-left: 1em;
}
.btn {
  margin-left: 2em;
}

.unit {
  width: 100%;
  margin-top: 1em;
  padding: 0.5em;
  color: black;
  background-color: darkgrey;
}
</style>
