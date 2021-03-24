<template>
    <div class="pt-4">
        <b-container id="app">
            <b-card title="Card Title" no-body>
                <b-card-header header-tag="nav">
                    <b-navbar>
                        <b-collapse is-nav>
                            <b-navbar-nav>
                                <b-nav-item>
                                    <router-link to="/">{{ $t("APP.NAV.HOME") }}</router-link>
                                </b-nav-item>
                                <b-nav-item>
                                    <router-link to="/about">About</router-link>
                                </b-nav-item>
                            </b-navbar-nav>

                            <b-navbar-nav class="ml-auto">
                                <b-nav-item-dropdown text="Lang" right>
                                    <b-dropdown-item @click="activeLanguage('fr')">
                                        {{ $t("APP.NAV.LANG.FR") }}
                                    </b-dropdown-item>
                                    <b-dropdown-item @click="activeLanguage('en')">
                                        {{ $t('APP.NAV.LANG.EN') }}
                                    </b-dropdown-item>
                                </b-nav-item-dropdown>
                            </b-navbar-nav>
                        </b-collapse>
                    </b-navbar>
                </b-card-header>

                <b-card-body>
                    <router-view />
                </b-card-body>
            </b-card>
        </b-container>
    </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component
export default class AppComponent extends Vue {

    public activeLanguage(lang: string) {
        this.$i18n.locale = lang;
    }
}
</script>


