<script >
import { RouterLink, RouterView } from 'vue-router'
export default {
    data() {
        return {
        }
    },
    components: {
        RouterLink,
        omg() {
            this.page = 1
        }
    },

}
</script>

<template>
    <nav class="topNav">
        <div class="toplink">
            <RouterLink to="/" class="oo1 link1">
                <i class="fa-solid fa-house"></i>
            </RouterLink>
            
                <div class="top" >
                    <i class="fa-solid fa-magnifying-glass">
                        <input type="text" class="text" >
                    </i>    
                </div>
        </div>

    </nav>

    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css" integrity="sha512-z3gLpd7yknf1YoNbCzqRKc4qyor8gaKU1qmn+CShxbuBusANI9QpRohGBreCFkKxLhei6S9CQXFEbbKuqLg0DA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
</template>

<style lang="scss" scoped>
.topNav {
    width: 98.8vw;
    height: 15vh;
    background-color: #bc84a8;
    font-size: 24pt;
    color: white;

}
.toplink {
    height: 100%;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-around;

    .oo1 {
        text-decoration: none;
        font-size: 30pt;
        color: white;
    }

    .link1 {
        &:hover {
            color: black;
        }
    }


}
.top{
    height: 50%;
    width: 30%;
    background-color: white;
    border-radius: 30%;
    display: flex;
    
    .fa-magnifying-glass{
        font-size: 20pt;
        // margin-left: 1%;
        color: black;
        display: flex;
        justify-content: space-around;
    align-items: center;
    }
.text{
    width: 80%;
    background:none ;
    border: none;
    
}
}

</style>
