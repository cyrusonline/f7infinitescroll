<template>
<f7-page infinite :infinite-distance="50" :infinite-preloader="showPreloader" @infinite="loadMore">
    <f7-navbar title="Infinite Scroll" back-link="Back"></f7-navbar>
    <f7-block-title>Scroll bottom </f7-block-title>

    <f7-list>
        <f7-list-item v-for="(item, index) in items" :title="item.data().year" :key="index"></f7-list-item>
    </f7-list>
</f7-page>
</template>

<script>
import firebase from 'firebase'
import {
    f7Navbar,
    f7Page,
    f7BlockTitle,
    f7List,
    f7ListItem
} from 'framework7-vue';
export default {
    components: {
        f7Navbar,
        f7Page,
        f7BlockTitle,
        f7List,
        f7ListItem,
    },
    data() {
        return {
            items: [],
            allowInfinite: true,
            showPreloader: true,
            pagesize: 10,
            cursor: {}
        };
    },
    methods: {

        // /paper2/77WObolYZBfVDlhVxAjF
        getItems() {
            const self = this;
            self.items = [];

            let query = firebase.firestore().collection('paper2').orderBy("year", "desc").limit(this.pagesize)
            query.get().then((docs) => {
                docs.forEach((doc) => {
                    this.items.push(doc)
                })
                this.cursor = this.items[this.items.length - 1]
            })

            // query.get()
            //     .then(docs => {
            //         docs.forEach(doc => {
            //           console.log(doc.data())
            //             // let paperOne = doc.data()
            //             // paperOne.id = doc.id
            //             // console.log(paperOne)
            //             this.items.push(doc);
            //         })

            //         self.cursor = self.items[self.items.length-1];

            //     }).catch(err => {
            //         // console.log(err)
            //     })
        },
        loadMore() {
            console.log('testing load more')
            const self = this;
            if (!self.allowInfinite) return;
        self.allowInfinite = false;
            let query = firebase.firestore().collection('paper2').orderBy("year", "desc").startAfter(this.cursor).limit(this.pagesize)
            query.get().then((docs) => {
                docs.forEach((doc) => {
                    console.log(doc.data().year)
                    this.items.push(doc)
                })

                if(docs.size<self.pagesize){
                  self.showPreloader = false
                   self.allowInfinite = false
                  return
                }

                // if (self.items.length >= self.pagesize) {
                //     self.showPreloader = false;
                //       return;
                // } else {
                //     self.
                // }
                self.allowInfinite = true;

            })
        }
        // loadMore() {
        //     console.log('load more')
        //     const self = this;
        //     if (!self.allowInfinite) return;
        //     self.allowInfinite = false;
        //     let query = firebase.firestore().collection('paper2').orderBy("year", "desc").startAfter(this.cursor).limit(this.pagesize)
        //     query.get()
        //         .then(docs => {
        //             docs.forEach((doc) => {
        //                 // let paperOne = doc.data()
        //                 // paperOne.id = doc.id
        //                 // console.log(paperOne)
        //                 this.items.push(doc);
        //             })

        //             self.cursor = self.items[self.items.length-1];

        //         }).catch(err => {
        //             // console.log(err)
        //         })

        // setTimeout(() => {
        //     if (self.items.length >= 200) {
        //         self.showPreloader = false;
        //         return;
        //     }
        //     const itemsLength = self.items.length;
        //     for (let i = 1; i <= 20; i += 1) {
        //         self.items.push(itemsLength + i);
        //     }
        //     self.allowInfinite = true;
        // }, 1000);
        // },
    },
    created() {
        this.getItems()
    }
};
</script>
