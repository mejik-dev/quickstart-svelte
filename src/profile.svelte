<script>
  import { onMount } from 'svelte';
  import { Link, navigate } from 'svelte-routing';
  import { microgen } from './lib/microgen';

  export let params;

  let firstName;
  let lastName;
  let phoneNumber;
  let email;
  let profileId;
  let image;
  let company;
  let position;
  let location;
  let website;
  let isOwnProfile = false;

  const getProfile = async () => {
    if (!params) {
      return;
    }

    const { data, error } = await microgen.service('Users').find({
      where: {
        username: params,
      },
      lookup: '*',
    });

    if (error) {
      console.log(error);
      return;
    }

    if (data) {
      const user = data?.[0];
      const profile = user?.profile?.[0];
      firstName = user.firstName;
      lastName = user.lastName;
      phoneNumber = user.phoneNumber;
      email = user.email;
      profileId = profile._id;
      image = profile.image;
      company = profile.company;
      location = profile.location;
      position = profile.position;
      website = profile.website;

      const { user: authUser } = await microgen.auth.user();

      isOwnProfile = user.username === authUser?.username;
    }
  };

  const handleLogout = async () => {
    const { error } = await microgen.auth.logout();

    if (error) {
      console.log(error);
      return;
    }

    navigate('/');
  };

  onMount(() => {
    getProfile();
  });
</script>

<div class="profile-page">
  {#if isOwnProfile}
    <div class="button-top">
      <Link to="/profile" class="button">Edit Profile</Link>
      <button on:click={handleLogout}>Logout</button>
    </div>
  {/if}
  <div class="profile-wrapper">
    <div class="profile-header">
      <img
        class="image-avatar"
        width="90"
        height="90"
        src={image || 'https://via.placeholder.com/90'}
        alt=""
      />
      <h3 class="profile-title">
        <span>{firstName}</span> <span>{lastName}</span>
      </h3>
      <p>{position || 'position is null'}</p>
    </div>
    <div class="card">
      <h3>Contact</h3>
      <div class="card-field">
        <span>Name</span>
        <p>{firstName} {lastName}</p>
      </div>
      <div class="card-field">
        <span>Mobile</span>
        <p>{phoneNumber || 'phone number is null'}</p>
      </div>
      <div class="card-field">
        <span>Email</span>
        <a class="link-email" href="'mailto:' + email">
          {email || 'email is null'}
        </a>
      </div>
      <div class="card-field">
        <span>Company</span>
        <p>{company || 'company is null'}</p>
      </div>
    </div>
    <div class="card">
      <h3>Location</h3>
      <p>{location || 'location is null'}</p>
    </div>
    <div class="card">
      <h3>Web Links</h3>
      <a class="website-link" href={website || ''}> Website </a>
    </div>
  </div>
</div>
