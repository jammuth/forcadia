<template>
  <div class="home">
    <!--meta data component-->
    <div class="list-metadata">
      <label for="listname">Enter List Name: </label>
      <input type="text" name="listname" id="listname" minlength="4" />
      <button class="btn">Save List</button>
    </div>

    <!--Unit Selection Component-->
    <div class="container">
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
      <p>Points: {{ pointsTotal.toString().padStart(4, "0") }} pts</p>
    </div>

    <!-- AmryList Component-->
    <div class="armylist">
      <div class="unit" v-for="unit in myUnits.units" :key="unit.id">
        <span class="unittitle"
          ><h2>
            {{
              `${unit.name.padEnd(50, ".")} +${unit.cost
                .toString()
                .padStart(2, "0")} pts`
            }}
          </h2>
          <button class="btn" @click="deleteUnit(unit)">x</button></span
        >
        <div v-if="unit.size">
          <select v-model="unit.unitsize">
            <option v-for="size in unit.size" :value="size">
              {{ size }}
            </option>
          </select>
        </div>

        <div v-if="unit.extra_type == 'each'" v-for="n in unit.unitsize">
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
                v-model="extra.cost"
                :value="option.cost"
                type="radio"
                :name="unit.id + ' ' + extra.id"
                :change="updateOption(extra, option)"
              />
              {{
                `${option.name.padEnd(30, ".")} +${option.cost
                  .toString()
                  .padStart(2, "0")} pts`
              }}
            </div>
          </div>
        </div>

        <div v-else v-for="extra in unit.extras">
          {{ extra.name }}

          <!--check-->
          <div
            :key="option.id"
            v-for="option in extra.options"
            v-if="extra.type == 'check'"
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
            :key="option.id"
            v-for="option in extra.options"
            v-if="extra.type == 'group'"
          >
            <input
              v-model="extra.cost"
              :value="option.cost"
              type="radio"
              :name="unit.id + ' ' + extra.id"
              :change="updateOption(extra, option)"
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
import { ref, reactive, computed } from "vue";
import pointsData from "../assets/points_list.json";
import clone from "just-clone";

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
const pointsTotal = computed(() => {
  return myUnits.units.reduce((p, c) => {
    const unitsize = "unitsize" in c ? c.unitsize : 1;

    const unitcost = c.cost * unitsize;

    const optionscost =
      "extras" in c
        ? c.extras.reduce((p2, c2) => {
            const cost =
              c2.type == "group"
                ? c2.cost
                : c2.options.reduce((prev, curr) => {
                    return curr.active ? prev + curr.cost : prev;
                  }, 0);
            return p2 + cost;
          }, 0)
        : 0;

    const extrascost = optionscost * (c.extra_type == "one" ? 1 : unitsize);

    return p + unitcost + extrascost;
  }, 0);
});

const getUnits = () => {
  unitsList.units = pointsData
    .filter((el) => el.role == selectedRole.value)
    .map((e) => e.name);

  selectedUnit.value = unitsList.units[0];
};

const getUnit = (name) => {
  return clone(pointsData.filter((el) => el.name == name)[0]);
};

const selectedUnit = ref(unitsList.units[0]);
getUnits();

const addUnit = () => {
  const unit = getUnit(selectedUnit.value);
  unit.id = myUnits.units.length;
  myUnits.units.push(unit);
};

const updateOption = (extra, option) => {
  console.log("not implemented");
};

const deleteUnit = (unit) => {
  myUnits.units = myUnits.units.filter((el) => el.id != unit.id);
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

.unittitle {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.unittitle button {
  height: fit-content;
  text-align: center;
}
</style>
