<template>
  <div class="h-full w-percent-100 max-h">
    <div class="card-header d-flex align-items-center pt-5">
      <span v-if="facility.isVirtual" class="bg-blue rounded-full badge mr-4" style="padding: 12px"></span>
      <span v-if="!facility.isVirtual" class="bg-red rounded-full badge mr-4" style="padding: 12px"></span>
      <h3 class="header-title" style="font-size: 2rem;line-height: 28px;">{{ facility.name }}</h3>
    </div>

    <div class="card-text">
      <p>
        <b>{{ facility.organisation }}</b>
      </p>
    </div>

    <div class="card-action">
      <div class="action-links d-flex align-items-center">
        <i class="icon icon-open-in-new mr-4"></i>
        <a v-if="isWebsite(facility.website) && facility.website" v-bind:href="getWebsiteLink(facility.website)" target="_blank" aria-label="Besøg facilitetens website">Besøg website</a>
        <a v-if="!isWebsite(facility.website) && facility.website" v-bind:href="getWebsiteLink(facility.website)" target="_blank" aria-label="Ring til faciliteten">{{facility.website}}</a>
      </div>
    </div>

    <div class="card-text">
      <p><b>Udbydertype:</b> {{ getProviders }}</p>
      <p><b>Kategori(er):</b> {{ getCategories }}</p>
      <p><b>Branche(r):</b> {{ getAreas }}</p>
    </div>

    <div v-if="showMore" class="card-text">
      <p><b>Service(s):</b> {{ getServices }}</p>
      <p><b>Brugerbetaling:</b> {{ getPayment }}</p>
      <p><b>Beskrivelse:</b> {{ facility.description }}</p>
    </div>

    <div v-if="showMore" class="card-action">
      <div class="action-links d-flex align-items-center">
        <i class="icon icon-location-on mr-4"></i>
        <a aria-label="Naviger til faciliteten" target="_blank" v-bind:href="`https://maps.apple.com/?daddr=${facility.city}+${facility.zip}+${facility.address}&dirflg=d`"
        >{{ facility.address }}, {{ facility.city }}, {{ facility.zip }}</a
        >
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Facility, ListItem, ProviderTypes, Categories, AreaTypes, ServiceTypes } from '../store/types';

export default {
  name: 'Facility',
  props: {
    showMore: {
      type: Boolean,
      default: false
    },
    facility: {
      type: Object as () => Facility
    }
  },
  computed: {
    getProviders(): string {
      return (
          this.facility.providerTypes
              ?.map((item: ListItem) => {
                const data = ProviderTypes[Number(item.value)];
                if (data) return `${data?.text}`;
              })
              ?.join(', ') ?? ''
      );
    },
    getCategories(): string {
      return (
          this.facility.categories
              ?.map((item: ListItem) => {
                const data = Categories[Number(item.value)];
                if (data) return `${data?.text}`;
              })
              ?.join(', ') ?? ''
      );
    },
    getAreas(): string {
      return (
          this.facility.areaTypes
              ?.map((item: ListItem) => {
                const data = AreaTypes[Number(item.value)];
                if (data) return `${data?.text}`;
              })
              ?.join(', ') ?? ''
      );
    },
    getServices(): string {
      return (
          this.facility.serviceTypes
              ?.map((item: ListItem) => {
                const data = ServiceTypes[Number(item.value)];
                if (data) return `${data?.text}`;
              })
              ?.join(', ') ?? ''
      );
    },
    getPayment(): string {
      if (this.facility.paymentType == 0) return 'Nej';
      if (this.facility.paymentType == 1) return 'Ja';
      if (this.facility.paymentType == 2) return 'I nogen tilfælde';
      return 'Ukendt';
    }
  },
  methods: {
    getWebsiteLink(url: string): string {
      const re = /^[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*$/;
      if (re.exec(url)) {
       return `tel:${url}`;
      }
      return url.startsWith('http') ? url : `https://${url}`;
    },
    isWebsite(url: string): boolean {
      const re = /^[+]*[(]{0,1}[0-9]{1,4}[)]{0,1}[-\s\./0-9]*$/;
      return !re.exec(url);
    }
  }
};
</script>

<style lang="scss" scoped>
@import '../styles/components/_external.scss';

.max-h {
  max-height: 500px;

  @media (min-width: map-get($grid-breakpoints, lg)) {
    max-height: 690px;
  }



}
</style>
