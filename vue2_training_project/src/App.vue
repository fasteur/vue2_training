<template>
    <div class="pt-4">

        <b-container id="app">
            <b-card title="Card Title" no-body>
                <b-card-header header-tag="nav">
                    <b-nav card-header tabs align="center">
                        <b-nav-item >
                            <router-link to="/">Home</router-link>
                        </b-nav-item>
                        <b-nav-item>
                            <router-link to="/about">About</router-link>
                        </b-nav-item>
                    </b-nav>
                </b-card-header>

                <b-card-body>
                    <router-view />
                </b-card-body>
            </b-card>
            
        </b-container>
    </div>
</template>