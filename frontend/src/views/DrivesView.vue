<template>
  <div
    v-if="!drives.length && !unsyncedDrives.length"
    class="alert alert-warning m-5"
    role="alert">
    {{ $t('drives.no_driver_drives') }}
  </div>
  <div
    v-else>

    <h4
      class="heading"
      v-if="unsyncedDrives.length"
    >
      {{ $t('drives.unsynced_drives') }}
    </h4>
    <p
      v-if="unsyncedDrives.length"
      class="ml-3"
    >
      {{ $t('drives.total_mileage', { total: unsyncedDrivesTotalMileage }) }}
    </p>
    <div
      class="card"
      v-for="drive in unsyncedDrives"
      :key="drive.id">
      <div
        class="card-header"
        @click="showDrive(drive.syncId)">
        <h5 class="mb-0">
          <span class="font-weight-bold">{{ drive.date }}</span>
          {{ $t('drives.from_to', { from: drive.startLocation, destination: drive.endLocation}) }}
        </h5>
      </div>
      <div :class="['collapse', { show: visibleDrive === drive.syncId }]">
        <div class="card-body">
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.description') }}</span>
            <span>{{ drive.description }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.car') }}</span>
            <span>{{ drive.car.plates }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.project') }}</span>
            <span>{{ drive.project.title }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.starting_mileage') }}</span>
            <span>{{ drive.startMileage }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.ending_mileage') }}</span>
            <span>{{ drive.endMileage }}</span>
          </p>
        </div>
      </div>
    </div>

    <h4
      class="heading mt-3"
      v-if="drives.length"
    >
      {{ $t('drives.synced_drives') }}
    </h4>
    <p
      v-if="drives.length"
      class="ml-3"
    >
      {{ $t('drives.total_mileage', { total: totalKilometers }) }}
    </p>
    <div
      class="card"
      v-for="drive in drives"
      :key="drive.id">
      <div
        class="card-header"
        @click="showDrive(drive.id)">
        <h5 class="mb-0">
          <span class="font-weight-bold">{{ drive.date }}</span>
          {{ $t('drives.from_to', { from: drive.startLocation, destination: drive.endLocation}) }}
        </h5>
      </div>
      <div :class="['collapse', { show: visibleDrive === drive.id }]">
        <div class="card-body">
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.description') }}</span>
            <span>{{ drive.description }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.car') }}</span>
            <span>{{ drive.car.plates }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.project') }}</span>
            <span>{{ drive.project.title }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.starting_mileage') }}</span>
            <span>{{ drive.startMileage }}</span>
          </p>
          <p>
            <span class="font-weight-bold mr-1">{{ $t('drives.ending_mileage') }}</span>
            <span>{{ drive.endMileage }}</span>
          </p>
        </div>
      </div>
    </div>
  </div>

</template>
<script>
import { mapActions, mapState, mapGetters } from 'vuex';
import { actions as apiActions, namespaces, UNSYNCHRONISED_DRIVES, UNSYNCHRONISED_DRIVES_TOTAL_MILEAGE } from '../store/constants';
import { totalMileageReducer } from '../utils';

export default {
  name: 'DrivesView',
  data() {
    return {
      visibleDrive: null,
    };
  },
  methods: {
    ...mapActions(namespaces.drives, [apiActions.fetchDrives]),
    showDrive(id) {
      this.visibleDrive = this.visibleDrive === id ? null : id;
    },
  },
  computed: {
    ...mapGetters({
      unsyncedDrives: UNSYNCHRONISED_DRIVES,
      unsyncedDrivesTotalMileage: UNSYNCHRONISED_DRIVES_TOTAL_MILEAGE,
    }),
    ...mapState(namespaces.drives, {
      drives: state => state.data || [],
      totalKilometers: state => state.data.reduce(
        totalMileageReducer,
        0,
      ),
    }),
  },
  created() {
    this[apiActions.fetchDrives]();
  },
};
</script>

<style scoped lang="scss">
@import "../scss/base";

.card-header {
  cursor: pointer;
}

.wrapper {
  @include m(2);
}

.heading {
  text-align: center;
}
</style>
