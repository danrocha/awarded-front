<template>
  <layout name="BooksLayout">
    <div>
      <breadcrumbs :prev-screen-params="prevScreenParams" award-type="books">{{
        book.title
      }}</breadcrumbs>
      <article>
        <header class="mb-2 flex sm:items-center">
          <h2 class="flex items-center flex-wrap inline-block">
            {{ book.title }}&nbsp;
            <book-authors
              :authors="book.booksBookAuthors.nodes"
              class="text-faded leading-none mr-2"
            />
          </h2>
        </header>
        <main>
          <spinner v-if="$apollo.loading" />
          <template v-else>
            <section id="book-details" class="sm:flex">
              <div class="mb-2 sm:mr-4">
                <book-cover
                  :image-url="book.imageUrl"
                  w="150"
                  :isbn="book.isbn10 || book.isbn13"
                  class="mb-2"
                />
                <book-links-shopping
                  :isbn="book.isbn10 || book.isbn13"
                  :kindle-asin="book.kindleAsin"
                />
              </div>
              <div class="sm:w-1/2">
                <truncate
                  :text="book.description"
                  type="html"
                  collapsed-text-class="text-faded mr-2"
                  action-class="title-link"
                  :length="300"
                  clamp="more"
                  less="less"
                  class="mb-4"
                />

                <p v-if="book.numPages" class="text-faded mb-2">{{ book.numPages }} pages</p>
                <!-- <book-links-ratings
                  :imdb-id="book.imdbId"
                  :tmdb-id="book.tmdbId"
                  :title="book.title"
                />-->
              </div>
            </section>
            <section id="book-nominations" class="pt-4">
              <div v-if="bookStats" class="text-faded mb-4 a-uppercase-info">
                {{ bookStats.nominations }}
                {{ bookStats.nominations | pluralize("nomination") }}
                <span v-if="bookStats.wins">
                  <span class="text-xs mx-1 text-gray-700">★</span>
                  {{ bookStats.wins }}
                  {{ bookStats.wins | pluralize("win") }}
                </span>
              </div>
              <div>
                <list-transition>
                  <div
                    v-for="(awardNominations, index) in nominationsByAward"
                    :key="index"
                    :data-index="index"
                    class="mb-4"
                  >
                    <book-nominations-by-award :nominations="awardNominations" />
                  </div>
                </list-transition>
              </div>
            </section>
          </template>
        </main>
      </article>
    </div>
  </layout>
</template>

<script>
import gql from "graphql-tag";
const groupBy = require("lodash.groupby");
import Layout from "@/layouts/Layout";
import Spinner from "@/components/Spinner";
import NominationListItem from "@/components/NominationListItem";
import BookCover from "@/components/BookCover";
import Truncate from "@/components/Truncate";
import BookNominationsByAward from "../components/BookNominationsByAward";
import ListTransition from "../components/ListTransition";
import BookLinksShopping from "@/components/BookLinksShopping";
import BookAuthors from "@/components/BookAuthors";

export default {
  name: "BookView",
  metaInfo() {
    const bookTitle = this.bookTitle;
    return {
      title: `${bookTitle} - Nominations and Wins`,
      meta: [
        {
          vmid: "description",
          name: "description",
          content: `${bookTitle}: nominations and wins in all Awards and Festivals.`
        }
      ]
    };
  },
  components: {
    BookNominationsByAward,
    Spinner,
    Truncate,
    BookCover,
    BookAuthors,
    BookLinksShopping,
    Layout,
    ListTransition
  },
  props: {
    bookId: {
      type: String,
      required: true
    },
    bookTitle: {
      type: String,
      required: true
    },
    awardType: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      book: {
        id: this.book_id,
        title: this.title,
        publicationYear: null,
        booksBookAuthors: {
          totalCount: 0,
          nodes: []
        },
        booksNominations: {
          nodes: []
        }
      },
      prevScreen: "",
      prevScreenParams: null
    };
  },
  apollo: {
    book: {
      query: gql`
        query booksBook($id: Int!) {
          booksBook(id: $id) {
            id
            title
            isbn10
            isbn13
            imageUrl
            goodreadsId
            description
            numPages
            kindleAsin
            booksBookAuthors {
              totalCount
              nodes {
                author {
                  id
                  name
                }
              }
            }
            booksNominations {
              totalCount
              nodes {
                ...booksNomination
                award {
                  id
                  nameLong
                  nameShort
                  isFestival
                }
                edition {
                  id
                  date
                  name
                }
              }
            }
          }
        }
        ${NominationListItem.fragments.booksNomination}
      `,
      variables() {
        return {
          id: Number(this.bookId)
        };
      },
      update(data) {
        this.book = data.booksBook;
        if (this.book.description) {
          this.book.description = this.book.description.replace(/<b>/g, "");
          this.book.description = this.book.description.replace(/<\/b>/g, "");
        }
        return this.book;
      }
    }
  },
  computed: {
    nominationsByAward() {
      return this.groupByAward(this.book.booksNominations.nodes);
    },
    bookStats() {
      if (this.$apollo.loading) return;
      let wins = 0;
      this.book.booksNominations.nodes.map(nomination => {
        if (nomination.isWinner) {
          return (wins += 1);
        }
      });
      return { nominations: this.book.booksNominations.totalCount, wins };
    },
    bookYear() {
      return this.book.publicationYear;
    }
  },
  methods: {
    groupByAward(nominations) {
      return Object.values(groupBy(nominations, "award.id"));
    }
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.prevScreen = from.fullPath || "/";
      vm.prevScreenParams = from.params || null;
    });
  }
};
</script>

<style lang="scss" scoped></style>