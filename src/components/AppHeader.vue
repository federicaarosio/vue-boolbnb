<script>
    export default {
    name: "AppHeader"
    }
</script>

<template>
    <nav class="navbar navbar-expand navbar-light bg-white shadow-sm py-3">
        <div class="container-fluid px-5">

            <RouterLink class="navbar-brand d-md-none" to="/">
                <img src="../assets/img/logoBoolBnB.png" height="60">
            </RouterLink>

            <RouterLink class="navbar-brand d-none d-md-inline-block" to="/">
                <img src="../assets/img/logoBoolBnB-desktop.png" height="60">
            </RouterLink>

            <ul class="navbar-nav ms-auto">
                <li class="nav-item dropdown">
                    <button type="button" class="btn border border-secondary rounded-start-5 rounded-end-5 d-flex py-2 border-opacity-50 mb-1" data-bs-toggle="dropdown" aria-expanded="false">
                        <div style="width: 15px;" class="me-2"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><path d="M0 96C0 78.3 14.3 64 32 64H416c17.7 0 32 14.3 32 32s-14.3 32-32 32H32C14.3 128 0 113.7 0 96zM0 256c0-17.7 14.3-32 32-32H416c17.7 0 32 14.3 32 32s-14.3 32-32 32H32c-17.7 0-32-14.3-32-32zM448 416c0 17.7-14.3 32-32 32H32c-17.7 0-32-14.3-32-32s14.3-32 32-32H416c17.7 0 32 14.3 32 32z"/></svg></div>
                        <img src="https://e7.pngegg.com/pngimages/717/24/png-clipart-computer-icons-user-profile-user-account-avatar-heroes-silhouette-thumbnail.png" width="25" height="">
                    </button>
                    <ul class="dropdown-menu dropdown-menu-end">
                        <li><a class="dropdown-item" href="http://127.0.0.1:8000/">Login</a></li>
                        <li><a class="dropdown-item" href="http://127.0.0.1:8000/register">Register</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </nav>
</template>

<style lang="scss" scoped>

</style>