<template>
  <span
    ><person-link
      :person-id="nominatedPerson.person.id"
      :person-name="nominatedPerson.person.name"
      award-type="movies"
      >{{ nominatedPerson.person.name }}</person-link
    ><span v-if="nominatedPerson.character" class="ml-1 text-faded">
      (as {{ nominatedPerson.character }})</span
    ></span
  >
</template>

<script>
import gql from "graphql-tag";
import PersonLink from "./PersonLink";
import PersonListItem from "./PersonListItem";
export default {
  name: "MoviesNominatedPerson",
  components: {
    PersonLink
  },
  props: {
    nominatedPerson: {
      type: Object,
      required: true
    }
  },
  fragments: {
    moviesNominatedPerson: gql`
      fragment moviesNominatedPerson on MoviesNominatedPerson {
        id
        character
        job {
          id
          name
        }
        person {
          ...moviesPerson
        }
        prize {
          id
          name
          order
          display
        }
        nomination {
          category {
            id
            name
          }
          award {
            id
            nameShort
          }
        }
        moviesNominatedPersonPrizes {
          totalCount
          nodes {
            prize {
              id
              name
              order
              display
              award {
                id
                nameShort
              }
              category {
                id
                name
              }
            }
          }
        }
      }
      ${PersonListItem.fragments.moviesPerson}
    `
  }
};
</script>

<style lang="scss" scoped></style>
