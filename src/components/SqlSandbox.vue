<script>
import initSqlJs from 'sql.js';
import { computed, ref } from 'vue';

export default {
  data() {
    return {
      query: '',
      columns: [],
      rows: [],
      feedback: '',
    };
  },
  methods: {
    async setup() {
      this.rows = [];
      this.columns = [];
      const SQL = await initSqlJs({
        // Required to load the wasm binary asynchronously. Of course, you can host it wherever you want
        // You can omit locateFile completely when running in node
        // locateFile: (file) => `https://sql.js.org/dist/${file}`,
        locateFile: (file) => `https://sql.js.org/dist/${file}`,
      });
      const db = new SQL.Database();
      // Create a table
      try {
        db.run(this.query);
        this.feedback = 'Query is valid' + ' -> ' + this.query;

        var result = db.exec('SELECT * FROM mytable');
        if (result.length != 0) {
          this.columns = result[0].columns;
          this.rows = result[0].values;
        } else {
          result = db.exec('PRAGMA table_info(mytable);');
          this.columns = result[0].values.map((row) => row[1]);
        }
      } catch (error) {
        this.feedback = error;
      }

      // db.run('CREATE TABLE mytable (id INTEGER PRIMARY KEY, name TEXT)');

      // // Insert some data into the table
      // db.run('INSERT INTO mytable (id, name) VALUES (?, ?)', [1, 'John']);
      // db.run('INSERT INTO mytable (id, name) VALUES (?, ?)', [2, 'Jane']);

      // Select all rows from the table
    },
  },
};
</script>

<template>
  <div class="hello">
    Now! try what you've learn using this sandbox.
    <br />
    Write a query that will create a <strong>table</strong> with a name of
    <strong>`mytable`</strong>, with an attribute of
    <strong>`id`</strong> DATA-TYPE <strong>INTEGER</strong> assigned as
    <strong>PRIMARY KEY</strong>, and another attribute of
    <strong>`name`</strong> DATA-TYPE <strong>TEXT</strong>.
    <br />
    <!-- <textarea v-model="query" @keyup.enter="setup()" rows="4" cols="70" /> -->
    <form @submit.prevent="setup">
      <textarea v-model="query" rows="5" cols="70" id="inputField" />
      <button type="submit">Submit</button>
    </form>
    <br />
    {{ feedback }}
    <br />
    <table>
      <thead>
        <tr>
          <th v-for="(row, index) in columns" :key="index">{{ row }}</th>
        </tr>
      </thead>
      <tbody v-for="(row, index) in rows" :key="index">
        <tr>
          <td>{{ row[0] }}</td>
          <td>{{ row[1] }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

th {
  padding: 5px;
  border-style: solid;
}

td {
  padding: 5px;
  border-style: solid;
}

textarea {
  resize: none;
}
</style>
