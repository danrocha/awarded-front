<template>
  <div></div>
</template>

<script>
import gql from "graphql-tag";
export default {
  fragments: {
    moviesPerson: gql`
      fragment moviesPerson on MoviesPerson {
        id
        tmdbId
        imdbId
        name
        gender
        biography
        birthday
        deathday
        alsoKnownAs
        adult
        homepage
        knownForDepartment
        placeOfBirth
        popularity
        profilePath
      }
    `
  }
};
</script>

<style lang="scss" scoped></style>
