<script>
  import { Router, Route } from 'svelte-routing';
  import Home from './home.svelte';
  import EditProfile from './edit-profile.svelte';
  import Profile from './profile.svelte';

  export let url = '';
</script>

<Router {url}>
  <Route path="/" component={Home} />
  <Route path="/profile" component={EditProfile} />
  <Route path=":username" let:params>
    <Profile params={params?.username} />
  </Route>
</Router>
